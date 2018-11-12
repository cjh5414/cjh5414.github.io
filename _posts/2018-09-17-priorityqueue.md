---
layout: post
title:  [Java] 우선순위 큐(Priority Queue) 와 Comparable, Comparator
tags:   ['Algorithm', 'Java']
---

## 우선순위 큐(Priority Queue)  

우선순위 큐는 먼저 들어간 데이터가 먼저 나오는 일반적인 큐와는 다르게 데이터를 꺼낼 때 우선순위가 가장 높은 데이터가 가장 먼저 나온다. 우선순위 큐는 힙을 이용하여 구현하는 것이 일반적이다. 데이터를 삽입할 때 우선순위를 기준으로 최대힙 혹은 최소힙을 구성하고 데이터를 꺼낼 때 루트 노드를 얻어낸다. 루트 노드를 삭제할 때는 빈 루트 노드 위치에 맨 마지막 노드를 삽입한 후 아래로 내려가면서 적절한 자리를 찾아서 옮긴다.  

<br/>  

## 예제  
Java에서 우선순위 큐를 이용하는 방법을 예제와 함께 알아보자.  
Student 라는 클래스가 있다. `name`과 `age` 속성을 가지고 있다. 나이를 기준으로 나이가 많은 순, 적은 순 으로 우선순위 큐를 구성해서 사용해 보려고 한다. 

우선순위가 한 가지의 속성으로만 결정될 필요는 없다. 새로운 속성 값을 추가하고 `Comparable`, `Comparator` 를 잘 구현해주면 여러가지의 조건으로 우선 순위를 결정할 수 있다.  


### Student 클래스  

```java
class Student {
    String name;
    int age;

    public Student(String name, int age) {
        this.name = name;
        this.age = age;
    }

    @Override
    public String toString() {
        return "이름 : " + name + ", 점수 : " + age;
    }
}
```  

### Comparable 구현  

Student 클래스에 `Comparable`의 `compareTo()` 를 오버라이딩 하여 구현한다. age 값을 기준으로 반환 값이 음수, 양수 인지에 따라 우선순위가 결정된다. 아래의 예제는 나이가 많은 순을 구현한 것이다. (나이가 많은 학생이 우선순위가 높다.)  

```java
class Student implements Comparable<Student> {
    String name;
    int age;

    public Student(String name, int age) {
        this.name = name;
        this.age = age;
    }

    @Override
    public int compareTo(Student target) {
        return this.age <= target.age ? 1 : - 1;
    }

    @Override
    public String toString() {
        return "이름 : " + name + ", 나이 : " + age;
    }
}
```  

### 우선순위 큐 생성  

```java
static PriorityQueue<Student> getPriorityQueueOfStudents() {
    PriorityQueue<Student> priorityQueue = new PriorityQueue<>();

    priorityQueue.offer(new Student("김철수", 20));
    priorityQueue.offer(new Student("김영희", 100));
    priorityQueue.offer(new Student("한택희", 66));
    priorityQueue.offer(new Student("이나영", 7));
    priorityQueue.offer(new Student("이혁", 43));
    priorityQueue.offer(new Student("안영희", 100));

    return priorityQueue;
}
```  
### 실행 결과  

```java
public static void main(String[] args) {
    PriorityQueue<Student> priorityQueue = getPriorityQueueOfStudents();

    // 나이가 많은 순으로 학생들 목록을 출력
    while (!priorityQueue.isEmpty())
        System.out.println(priorityQueue.poll());
}
```  

```
나이가 많은 순.
이름 : 김영희, 나이 : 100
이름 : 안영희, 나이 : 100
이름 : 한택희, 나이 : 66
이름 : 이혁, 나이 : 43
이름 : 김철수, 나이 : 20
이름 : 이나영, 나이 : 7
```  

### 나이가 적은 순  

위에서 구현한 `compareTo()` 함수를 수정하여 구현할 수도 있지만 `Comparator` 를 사용하여 `compare()` 함수를 오버라이딩 하여 구현해 보았다.  `Comparable`는 인터페이스 이고 `Comparator`는 클래스이다. `Comparator`는 아래와 같이 특정 클래스의 생성자에 매개변수로 넣어서 구현할 수도 있고 특정 함수의 매개변수로 이용할 수도 있다.   

```java
priorityQueue = getPriorityQueueOfStudents();
PriorityQueue<Student> reversedPriorityQueue = new PriorityQueue<>(priorityQueue.size(), new Comparator<Student>() {
    @Override
    public int compare(Student p1, Student p2) {
       return p1.age >= p2.age ? 1 : -1;
    }
});
```  

람다식을 이용하면 아래와 같이 간단하게 표현 가능하다.  

```java
PriorityQueue<Student> reversedPriorityQueue = new PriorityQueue<>(priorityQueue.size(),
        (Student p1, Student p2) -> p1.age >= p2.age ? 1 : -1);
```   

### 발생 가능한 에러  

만약 Student 클래스에 `Comparable`를 구현하지 않고 PriorityQueue에 넣으려고 한다면 아래와 같은 에러가 발생한다.  

```
Exception in thread "main" java.lang.ClassCastException: Student cannot be cast to java.lang.Comparable
```  

