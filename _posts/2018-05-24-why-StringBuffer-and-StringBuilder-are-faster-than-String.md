---
layout: post
title:  StringBuffer, StringBuilder 가 String 보다 빠른 이유와 원리
tags:   ['Java', 'Algorithm']
---

> Java의 String, StringBuffer, StringBuilder 에 대해 검색을 해보면 어떤 것이 빠른지, 장단점은 무엇인지에 대해 정리해놓은 글들을 많이 찾아볼 수 있습니다. 하지만 "왜?", "어떻게?"를 구체적으로 설명하는 글은 없어서 MyStringBuilder를 직접 구현해보면서 알고리즘적으로 어떻게 다르게 구현되어있는지 정리했습니다.  

<br/>  

## String, StringBuffer, StringBuilder  

우선 Java에서 각각의 특징과 차이는 무엇인지 요약하자면,

<br/>  

## 문자열을 추가하는 연산에서 String가 느린 이유   

<br/>  

## StringBuffer, StringBuilder 는 이 문제점을 어떻게 개선하였는가?  

<br/>  

## Big-O 시간 복잡도

(ArrayList) 에도 동일한 개념 사용

<br/>  

## MyStringBuilder  

<br/>

## 참고자료  

- 코딩 인터뷰 완전 분석 - 게일 라크만 맥도웰 지음, 이창현 옮김    
- [Java8 StringBuilder Source code](http://grepcode.com/file/repository.grepcode.com/java/root/jdk/openjdk/6-b14/java/lang/StringBuilder.java)  
