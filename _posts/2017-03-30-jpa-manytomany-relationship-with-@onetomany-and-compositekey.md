---
layout: post
title:  JPA 다대다 관계 모델 @OneToMany로 구현하여 Column 추가하기(@IdClass 사용)
tags:   [Java, JPA]
---

> JPA에는 다대다 관계를 구현할 수 있는 @ManyToMany 가 있다. 각각의 모델에 @ManyToMany 를 지정하면 두 테이블의 primary key를 복합키로 설정하여 관계 테이블을 알아서 생성한다. 하지만 이렇게 생성된 관계 테이블에는 column을 추가할 수 없기 때문에 실제로 사용하기는 어렵다. 관계 테이블을 class로 정의하고 @OneToMany, @ManyToOne 을 이용하여 다대다 관계를 맺어서 구현하면 column을 추가할 수 있고 관계 테이블의 repository도 생성할 수 있다.  

`Meeting` 이라는 테이블과 `User` 라는 테이블이 있다. 두 테이블 간의 `Participation` 관계 테이블을 넣어서 이용하려고 한다.  

![diagram](/images/jap-manytomany/diagram.png)  

<br/>  

### `Meeting`, `User` table

두 테이블 모두 `List` 를 이용해서 OneToMany 관계로 `Participation` 과 관계를 맺어준다.  

_model/Meeting.java_  

```java
@Entity
public class Meeting {
    @Id
    @Column(name="id")
    @GeneratedValue(strategy=GenerationType.AUTO)
    private int id;
    @Column(name="name")
    private String name;

    // ...

    @OneToMany(mappedBy = "meeting")
    private List<Participation> participationList = new ArrayList<>();
```  

_model/User.java_  

```java
@Entity
public class User {
    @Id
    @Column(name="id")
    private int id;
    @Column(name="name")
    private String name;
    @Column(name="password")
    private String password;

    // ...

    @OneToMany(mappedBy = "user")
    private List<Participation> participationList = new ArrayList<>();
```  

<br/>  

### `Participation` relationship table  

`Participation` 에서는 반대로 두 테이블을 ManyToOne 관계로 맺어준다. `Meeting` 과 `User` 의 id를 합쳐서 복합키로 지정한다. `@IdClass()` 을 이용하면 복합키를 하나의 클래스로 정의하여 관리할 수 있다. 아래에 나오는 `ParticipationId` 을 복합키로 지정한다.   

_model/Participation.java_  

```java
@Entity
@IdClass(ParticipationId.class)
public class Participation{
    @Id
    @ManyToOne
    @JoinColumn(name = "meeting_id")
    private Meeting meeting;
    @Id
    @ManyToOne
    @JoinColumn(name = "user_id")
    private User user;
```  

이제 `Participation` 에 column을 추가할 수 있다. 맴버변수를 추가하여 `@JoinColumn` 을 지정해주면 DB의 `Participation` 테이블에 필드가 생성된다.  

_model/ParticipationId.java_  

```java
public class ParticipationId implements Serializable {
    private int meeting;
    private int user;

    public ParticipationId() { }
    public ParticipationId(int meeting, int user) {
        this.meeting = meeting;
        this.user = user;
    }
}
```  

`ParticipationId` 는 복합키를 클래스로 구현한 것인데 이 것을 사용하지 않고도 `Participation` 의 복합키를 지정할 수 있지만 `ParticipationRepository`는 생성할 수 없다. `Serializable`을 상속받아서 복합키를 구현해야지만 아래와 같이 `CrudRepository`의 두번째 type으로 지정해서 `ParticipationRepository`를 사용할 수 있다.  

_repository/ParticipationRepository.java_  

```java
public interface ParticipationRepository extends CrudRepository<Participation, ParticipationId> { }
```  
