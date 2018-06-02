---
layout: post
title:  StringBuffer, StringBuilder 가 String 보다 빠른 이유와 원리
tags:   ['Java', 'Algorithm']
---

> Java의 String, StringBuffer, StringBuilder 에 대해 검색을 해보면 어떤 것이 빠른지, 장단점은 무엇인지에 대해 정리해놓은 글들을 많이 찾아볼 수 있습니다. 하지만 "왜?", "어떻게?"를 구체적으로 설명하는 글은 없어서 MyStringBuilder를 직접 구현해보면서 알고리즘적으로 어떻게 다르게 구현되어있는지 정리했습니다.  

<br/>  

## String, StringBuffer, StringBuilder  

우선 Java에서 각각의 클래스의 특징과 차이가 무엇인지 요약하자면,

<br/>  

## 문자열을 추가하는 연산에서 String가 느린 이유   

문자열을 추가하는 연산에서 String 클래스가 느린 이유는 String 클래스의 `immutable` 특징 때문입니다. `immutable` 이란 변경할 수 없는, 불변의 라는 뜻으로 String 의 value 값이 한 번 생성되면 변경될 수 없습니다.

> The String class represents character strings. All string literals in Java programs, such as "abc", are implemented as instances of this class.
Strings are constant; their values cannot be changed after they are created. String buffers support mutable strings. Because String objects are immutable they can be shared. For example:

<br/>  

## StringBuffer, StringBuilder 는 이 문제점을 어떻게 개선하였는가?  

<br/>  

## Big-O 시간 복잡도

(ArrayList) 에도 동일한 개념 사용

<br/>  

## MyStringBuilder  

위에서 설명한 방법을 이용하여 StringBuilder 의 핵심적인 부분만 간단히 구현해보면 String 과 비교했을 때 성능이 매우 개선된 것을 확인해볼 수 있습니다.   

```java
import java.util.ArrayList;
import java.util.Arrays;

class MyStringBuilder {
    int size = 0;
    char[] value;

    public MyStringBuilder(String string) {
        value = new char[string.length() + 16];
        string.getChars(0, string.length(), value, 0);
        size = string.length();
    }

    @Override
    public String toString() {
        return String.valueOf(value);
    }

    public void append(String string) {
        int oldSize = size;
        size = oldSize + string.length();
        if(size > value.length)
            value = Arrays.copyOf(value, value.length * 2);
        string.getChars(0, string.length(), value, oldSize);
    }

    public static void main(String[] args) {
        long startTime = 0;
        long endTime = 0;

        String string = new String("abc");
        StringBuilder stringBuilder = new StringBuilder("abc");
        MyStringBuilder myStringBuilder = new MyStringBuilder("abc");

        startTime = System.nanoTime();
        for (int i=0; i<10000; i++) {
            string += "abc" + i;
        }
        endTime = System.nanoTime();

        System.out.println("String 실행 시간          : " + (endTime - startTime));

        startTime = System.nanoTime();
        for (int i=0; i<10000; i++) {
            stringBuilder.append("abc");
        }
        endTime = System.nanoTime();

        System.out.println("StringBuilder 실행 시간   : " + (endTime - startTime));

        startTime = System.nanoTime();
        for (int i=0; i<10000; i++) {
            myStringBuilder.append("abc");
        }
        endTime = System.nanoTime();

        System.out.println("MyStringBuilder 실행 시간 : " + (endTime - startTime));
    }
}
```  

### 실행 결과  

```java
$ java MyStringBuilder
String 실행 시간          : 744113349
StringBuilder 실행 시간   : 438078
MyStringBuilder 실행 시간 : 976512
```  

<br/>

## 참고자료  

- 코딩 인터뷰 완전 분석 - 게일 라크만 맥도웰 지음, 이창현 옮김    
- [Java8 StringBuilder Source code](http://grepcode.com/file/repository.grepcode.com/java/root/jdk/openjdk/6-b14/java/lang/StringBuilder.java)  
