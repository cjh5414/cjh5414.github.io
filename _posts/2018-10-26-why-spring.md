---
layout: post
title:  Spring 이란? 왜 Spring을 사용하는가?
tags:   ['Spring']
---

## 스프링이란?  

스프링 프레임워크(Spring Framework)란 자바 플랫폼을 위한 오픈소스 애플리케이션 프레임워크로서  
동적인 웹 사이트를 개발하기 위한 여러 가지 서비스를 제공하고 있다고 한다. 스프링을 사용하므로써 이미 잘 짜여진 구조 위에서 핵심 로직에만 집중할 수 있어서 높은 생산성과 높은 코드 품질의 좋은 소프트웨어를 개발할 수 있다.

> 좋은 소프트웨어란 기능적 품질과 구조적 품질이 높은 소프트웨어를 좋은 소프트웨어라고 말할 수 있다. 기능적 품질이란 사용자고 만족하고 사용자가 원하는 것을 해주는 것을 의미하고 구조적 품질이란 잘 설계되고 잘 만들어져서 유지보수성, 재사용성과 확장성이 높은 것을 의미한다.    

<br/>  

## 스프링 주요 특징  

### IOC (Inversion Of Control)   
제어의 추제가 개발자가 아니라 프레임워크라는 뜻이다. 프레임워크에서 기본적인 골격을 잡아 놓아서 개발자는 그 제어의 흐름에 맞게 코드를 작성하고 프레임워크에서 개발자가 작성한 코드를 호출한다.

> 라이브러리와의 차이점은 라이브러리는 코드의 재사용성을 위한 클래스의 집합으로 주체가 개발자이다. 개발자가 라이브러리 함수를 호출하여 사용한다. 웹 프레임워크가 라이브러리를 포함한다고 볼 수 있다.  

<br/>  

### POJO (Plain Old Java Object)  

POJO는 자바의 객체지향적인 특징을 살려 비지니스 로직에 충실한 개발이 가능하도록 한다. POJO를 사용함으로써 더욱 심플하게 코드를 작성할 수 있고 테스트를 유연하게 할 수 있는 장점이 있다.  

아래 링크는 POJO 에 대한 좋은 글이 있어서 첨부한 글이다. POJO의 역사부터 올바르게 POJO를 사용하는 방법 등과 코드 예시와 함께 구체적인 설명까지 잘 설명되어있다. 다만, 글을 완벽하게 이해하기 위해서는 웹 개발에 대한 전반적인 지식과 스프링, 하이버네이트에 대한 개념이 요구된다.  
- http://itewbm.tistory.com/entry/POJOPlain-Old-Java-Object  

아래에 나오는 DI 와 AOP 개념도 POJO에 기반한 특징들이다.  

<br/>  

### DI (Dependency injection)   

### AOP (Aspect Orientated Programming)   

<br/>  

## 참고자료  

- https://ko.wikipedia.org/wiki/%EC%8A%A4%ED%94%84%EB%A7%81_%ED%94%84%EB%A0%88%EC%9E%84%EC%9B%8C%ED%81%AC
