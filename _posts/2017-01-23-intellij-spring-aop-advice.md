---
layout: post
title:  "Spring AOP advice Error in IntelliJ"
tags:   [Spring]
---

> IntelliJ에서 spring 프로젝트를 생성하고 xml로 AOP advice를 적용하여 실행하면 에러가 발생한다. aspects 라이브러리는 spring 프로젝트를 생성할 때 기본으로 추가 되지만 advice를 사용하기 위해서는 aspectjweaver 를 추가로 설치해줘야한다.

## AOP Example  

```xml
<bean id="logger" class="kr.ac.hansung.spring.aop.Logger">
</bean>

<aop:config>
    <aop:aspect id="myAspect" ref="logger">
        <aop:pointcut id="selectSound" expression="execution(void kr.ac.hansung.spring.aop.*.sound())" />
        <aop:before method="aboutToSound" pointcut-ref="selectSound" />
    </aop:aspect>
</aop:config>
```

## Error Message  

```
경고: Exception encountered during context initialization - cancelling refresh attempt: org.springframework.beans.factory.BeanCreationException: Error creating bean with name 'org.springframework.context.event.internalEventListenerProcessor': BeanPostProcessor before instantiation of bean failed; nested exception is org.springframework.beans.factory.BeanCreationException: Error creating bean with name 'org.springframework.aop.aspectj.AspectJPointcutAdvisor#0': Cannot create inner bean '(inner bean)#5e57643e' of type [org.springframework.aop.aspectj.AspectJMethodBeforeAdvice] while setting constructor argument; nested exception is org.springframework.beans.factory.BeanCreationException: Error creating bean with name '(inner bean)#5e57643e': Failed to introspect bean class [org.springframework.aop.aspectj.AspectJMethodBeforeAdvice] for lookup method metadata: could not find class that it depends on; nested exception is java.lang.NoClassDefFoundError: org/aspectj/lang/JoinPoint
Exception in thread "main" org.springframework.beans.factory.BeanCreationException: Error creating bean with name 'org.springframework.context.event.internalEventListenerProcessor': BeanPostProcessor before instantiation of bean failed; nested exception is org.springframework.beans.factory.BeanCreationException: Error creating bean with name 'org.springframework.aop.aspectj.AspectJPointcutAdvisor#0': Cannot create inner bean '(inner bean)#5e57643e' of type [org.springframework.aop.aspectj.AspectJMethodBeforeAdvice] while setting constructor argument; nested exception is org.springframework.beans.factory.BeanCreationException: Error creating bean with name '(inner bean)#5e57643e': Failed to introspect bean class [org.springframework.aop.aspectj.AspectJMethodBeforeAdvice] for lookup method metadata: could not find class that it depends on; nested exception is java.lang.NoClassDefFoundError: org/aspectj/lang/JoinPoint
```  

## Install aspectjweaver with maven  

```xml
<dependencies>
    <!-- aspectj -->
    <dependency>
        <groupId>org.aspectj</groupId>
        <artifactId>aspectjweaver</artifactId>
        <version>1.6.11</version>
    </dependency>
</dependencies>
```
