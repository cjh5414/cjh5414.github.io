---
layout: post
title:  foreach를 사용하여 Java List의 element를 제거할 때 발생하는 문제
tags:   ['Java']
---

> Java 에서 List에서 특정 값을 제거하고 싶을 때 foreach를 사용하면 `java.util.ConcurrentModificationException: null` 라는 에러가 발생할 수 있다.  

### Example  

`List<Integer>` 객체를 생성하여 몇가지 값들을 추가한 후에 `foreach`를 사용해서 Integer 의 값이 0이면 제거하도록 하는 간단한 예제 코드가 있다.  

```java
List<Integer> list = new ArrayList<Integer>();

// ... Add Integer elements in list

for (Integer i : list) {
        if(i==0) {
            list.remove(i);
        }
    }
}
```  

실행해보면 에러가 발생한다.  

```
java.util.ConcurrentModificationException: null
	at java.util.ArrayList$Itr.checkForComodification(ArrayList.java:901) ~[na:1.8.0_111]
	at java.util.ArrayList$Itr.next(ArrayList.java:851) ~[na:1.8.0_111]
```  

[Java Collection의 removeIf](https://docs.oracle.com/javase/8/docs/api/java/util/Collection.html#removeIf-java.util.function.Predicate-) 를 사용하면 간단하게 해결할 수 있다.  

```java
List<Integer> list = new ArrayList<Integer>();

// ... Add Integer elements in list

list.removeIf( i -> i == 0 );
```  

List의 `removeIf` 함수를 사용해서 매개변수로 'i가 0일 경우''라는 조건을 Predicate 인터페이스로 정의해주는 것으로 코드를 간단하게 작성할 수 있다.  
