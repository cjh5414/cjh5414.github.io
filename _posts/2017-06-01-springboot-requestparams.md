---
layout: post
title:  Spring @RequestParam 이용하여 form 데이터 controller로 받기
tags:   ['Spring']
---

> Spring의 Controller에서 `@RequestParam` 어노테이션을 이용하면 Http 요청의 데이터를 메소드의 파라미터로 받을 수 있다. html 태그에 `name`으로 지정한 값으로 매칭되는 방식이다. 이 외에도 파라미터 맵핑 방식에는 `@RequestHeader` `@RequestBody` 등이 있다.  

<br/>  

## Example  

input 태그의 `name` 속성을 `payment_participant_list`으로 지정하고 Controller에서 값을 넘겨 받을 파라미터에 `@RequestParam` 어노테이션을 지정해줘서 사용하는 예시이다.  

```
<form class="modal-content" th:action="@{'/meetings/' + ${meeting.id} + '/payment'}" method="post">
    ...
    <input th:each="participant : ${payment_participant_list}" type="checkbox" name="payment_participant_list" th:value="${participant.ssoId}" th:text="${participant.name}" checked="checked"/>
```   

```java
@PostMapping(value = "/{id}/payment/{paymentId}/update")
public String updatePayment(@PathVariable int id,@PathVariable int paymentId, Payment payment, User payer, @RequestParam List<User> payment_participant_list) {
    System.out.println(payment_participant_list);
}
```   

<br/>  

## Error  

Controller에 파라미터 `@RequestParam`와 같은 맵핑 방식을 지정하지 않고 실행하면 아래와 같은 에러가 발생한다.  

```
Whitelabel Error Page
This application has no explicit mapping for /error, so you are seeing this as a fallback.
Tue May 30 23:40:29 PWT 2017
There was an unexpected error (type=Internal Server Error, status=500).
Failed to instantiate [java.util.List]: Specified class is an interface
```  

```
org.springframework.beans.BeanInstantiationException: Failed to instantiate [java.util.List]: Specified class is an interface
	at org.springframework.beans.BeanUtils.instantiateClass(BeanUtils.java:99)
    ....
```
