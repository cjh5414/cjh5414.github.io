---
layout: post
title:  Spring AOP
tags:   [Spring]
---

> 프로그래밍을 할 때 핵심 기능(Business Logic)을 작성하면서 디버깅을 위한 log, 보안, 원자성을 위한 Transaction 등의 코드를 작성할 일이 많이 있다. 이러한 코드들을 핵심 기능과 함께 작성하는 것은 의존성을 높이고 재사용성을 낮추기 때문에 바람직하지 않다. Spring에서는 이런한 코드들을 Aspect로 분리하여 작성할 수 있도록 AOP라는 개념을 제공한다. 이 포스트의 예제에서는 AspectJ를 사용한다.  

<br/>  

## AOP(Aspect Oriented Programming)  

Business logic에 함께 작성되는 Cross cutting concerns(Logging, Transaction, Security 등과 같이 공통적으로 들어가는 코드)를 별도의 Aspect로 분리하여 개발함으로써 의존성을 약하게 하고, 코드의 반복을 줄여서 재사용성을 높일 수 있다.    

### AOP-Weaving  

Aspect는 Spring Framework가 Run time 시에 Proxy의 객체를 하나 만들어서 동적으로 호출함으로써 동작한다.  

<br/>  

## AOP 구성요소  

### JoinPoint  

Cross cutting concerns이 삽입되어 동작할 수 있는 위치이다. 하지만 해당 위치에 Joinpoint라는 것을 알려주기 위한 코드를 작성하지 않기 때문에 의존성이 약하다.   

```java
public class Cat implements Animal {
    private String name;

    [...]

    public void sound() {
        System.out.println("Cat " + name + " Meow~");
    }
}
```  
위의 코드에서 sound()에 해당한다.  


### Advice  

JoinPoint에 삽입되어 실행되는 Cross cutting concerns 이다.  
실행되는 시점에 따라 몇 가지 종류가 있다.  

- __before__ : 메소드가 실행되기 전.
- __after__ : 결과에 상관 없이 메소드가 실행된 후.
- __after-returning__ : 메소드가 정상적으로 return한 경우.
- __after-throwing__ : 메소드가 예외를 던지고 종료한 후.
- __around__ : 메소드 호출 전, 후 예외 발생 등 모든 시점.

```java
@Aspect
public class logger {

    [...]

    @Before("selectSound()")
    public void aboutToSound() {
        System.out.println("Before : about to sound");
    }
}
```  

메소드가 실행되기 전 `Before : about to sound`라는 로그를 출력하는 예시이다.  


### Pointcut   

어떤 클래스의 어떤 함수를 JoinPoint로 사용할 것인지를 지정하는 부분이다.  
```java
@Aspect
public class logger {
    @Pointcut("execution(void com.example.spring.aop.*.sound())")
    private void selectSound() { }

    [...]
}    
```  
모든 클래스의 sound()함수를 JoinPoint로 지정하는 예시이다. Pointcut의 이름은 `@Pointcut` 아래에 정의한 `selectSound` 함수와 같이 지정한다.  


### Aspect  

Advice와 Pointcut을 포함하고 있는 Class이다.  

```java
import org.aspectj.lang.annotation.Aspect;

@Aspect
public class logger {
    [...]
}    
```  
