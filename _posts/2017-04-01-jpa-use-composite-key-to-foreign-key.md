---
layout: post
title:  JPA Composite key(복합키)를 foreign key(외래키)로 사용하기
tags:   [JPA, Java]
---

> 이전 포스트 [JPA 다대다 관계 모델 @OneToMany로 구현하여 Column 추가하기(@IdClass 사용)](https://cjh5414.github.io/jpa-manytomany-relationship-with-@onetomany-and-compositekey/) 에서 두 테이블의 관계테이블을 구현하는데 `@IdClass`를 이용해서 두 테이블의 id를 Composite Key(복합키)로 지정했었다. 이번 포스트에서는 JPA에서 Composite key(복합키)를 사용하고 있는 테이블에 foreign key(외래키)로, OneToMany 관계를 맺는 테이블을 구현하는 방법에 대해서 다룬다.  

<br/>  

### EER Diagram  

![diagram](/images/jpa-composite-foreign/diagram.png)  

`user`, `meeting`의 관계테이블인 `participation`이 있다. 두 테이블의 id를 composite key로 사용하고 있는데 이 테이블과 `payment`라는 테이블이 OneToMany로 관계를 맺도록 JPA로 model을 구현하고자 한다.  

<br/>  

### JPA Models  

관계테이블 `participation`의 model 구현 코드이다.  

_model/ParticiPationId.java_   

```java
public class ParticipationId implements Serializable {
    private int meeting;
    private String user;

    public ParticipationId() { }
    public ParticipationId(int meeting, String user) {
        this.meeting = meeting;
        this.user = user;
    }
}
```  

`ParticipationId` class는 `Participation`의 복합키를 `@IdClass` 을 사용하여 구현하기 위해 필요하다.  

_model/participation.java_   

```java
@Entity
@IdClass(ParticipationId.class)
public class Participation {
    @Id
    @ManyToOne
    @JoinColumn(name="meeting_id")
    private Meeting meeting;
    @Id
    @ManyToOne
    @JoinColumn(name="user_id")
    private User user;
    @OneToMany(mappedBy="participation")
    private List<Payment> paymentList = new ArrayList<>();
```  

`Participation` model에서는 복합키로 사용되는 두 테이블의 id를 ManyToOne 관계로 지정하기 위한 코드가 작성돼 있고, `Payment` model을 OneToMany로 설정한 내용이다.  

_model/Payment.java_  

```java
@Entity
public class Payment {
    @Id
    @Column(name="id")
    @GeneratedValue(strategy=GenerationType.AUTO)
    private int id;
    @ManyToOne
    @JoinColumns({
            @JoinColumn(name="meeting_id"),
            @JoinColumn(name="user_id")
    })
    private Participation participation;

    // ...

    public Payment() {}
}
```  

`payment` 테이블은 id를 따로 가지고 있고 `participation` 테이블을 외래키로 참조하고 있다. 그리고 그 외래키가 복합키(`participation`의 primary key인 `user`, `meeting`의 id)를 참조하고 있다. 이를 구현하기 위해 `Payment` model에서는 participation에 ManyToOne 관계를 맺고, JoinColumns으로 복합키의 id들을 설정해줘야한다.  

<br/>  

### 추가로 더 알고싶다면  

`user`, `meeting` 과의 관계테이블을 구성하는 방법은 [JPA 다대다 관계 모델 @OneToMany로 구현하여 Column 추가하기(@IdClass 사용)](https://cjh5414.github.io/jpa-manytomany-relationship-with-@onetomany-and-compositekey/) 에 나와있다.  
