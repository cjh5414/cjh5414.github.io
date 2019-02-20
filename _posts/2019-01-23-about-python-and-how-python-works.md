---
layout: post
title:  Python에 대하여, Python은 어떻게 동작하는가? Python의 장단점
tags:   ['Python']
---

## 컴파일 언어와 비교한 인터프리터 언어  

Python에 대해 알아보기에 앞서 인터프리터에 대해 간략이 알아보자.  
소스 코드를 기계어로 컴파일해서 실행파일을 만들고 실행하는 컴파일 언어와는 다르게 인터프러터 언어는 코드를 한 줄씩 읽어 내려가며 실행하는 프로그래밍 언어이다. 인터프리터 방식을 쓰는 프로그래밍 언어는 많은데 대표적인 것이 Python이다.   

### 인터프리터 언어의 장점   
인터프리터는 실행 시마다 소스 코드를 한 줄씩 기계어로 번역하는 방식이기 때문에 실행 속도는 컴파일 언어보다 느리다. 그렇다면 왜 사용할까? 인터프리터 언어는 프로그램 수정이 간단하다는 장점이 있다. 컴파일러는 소스 코드를 번역해서 실행 파일을 만들기 때문에 프로그램에 수정 사항이 발생하면 소스 코드를 다시 컴파일해야 한다. 하지만 인터프리터는 소스 코드를 수정해서 바로 실행시킬 수 있다.

> 인터프린터에 대한 더 자세한 애용은 아래의 링크를 참고하길 바란다.  
[인터프리터 | 나무위키](https://namu.wiki/w/%EC%9D%B8%ED%84%B0%ED%94%84%EB%A6%AC%ED%84%B0)  

<br/>  

## Python  

Python은 1991년 프로그래머인 귀도 반 로섬(Guido van Rossum)이 발표한 고급 프로그래밍 언어로, 플랫폼 독립적이며 인터프리터식, 객체지향적, 동적 타이핑(dynamically typed) 대화형 언어이다. 파이썬이라는 이름은 귀도가 좋아하는 코미디 〈Monty Python's Flying Circus〉에서 따온 것이다.   

> 출처 : [Python | wikipedia](https://ko.wikipedia.org/wiki/%ED%8C%8C%EC%9D%B4%EC%8D%AC)  

Python은 읽고 쓰기 쉽고 배우기 편한 언어로 잘 알려져 있다. 아래의 링크에 파이썬의 장점에 대해 잘 설명되어 있다.  

> [파이썬의 특징](https://wikidocs.net/6)  

<br/>  

## Python은 어떻게 동작하는가?      

### Python의 구현체 CPython  
일반적으로 python이 C로 구현되어 있다고 알려져있는데 그 구현체가 CPython이다. 가장 처음 만들어진 python의 구현체이고 _Python.org_ 에서 다운 받으면 CPython을 받는 것이다. 다른 구현체들과 비교해서 언급할 때 주로 `CPython`이라고 표기하는데 Python을 C언어로 구현한 구현체를 의미한다.

CPython은 인터프리터 이면서 컴파일러 이다. 우리가 작성하는 Python 코드를 bytecode로 컴파일 하고 실행한다. 다시 말해, python 코드를 C언어로 바꾸는 것이 아니라 컴파일하여 bytecode로 바꾸고 그 다음에 interpreter(virtual machine)가 실행한다.

`.py` 파일을 실행하면 `.pyc` 라는 파일이 생성되는데 이것이 CPython이 컴파일한 bytecode가 들어있는 것이다. 그 다음 `.pyc`를 interpret 하는 것도 CPyton이다.

`dis` 라는 패키지를 사용하면 python 코드가 어떻게 bytecode로 변환되는지를 볼 수 있다.  

```
>>> def hello():
...    print("Hello")  
...
>>> import dis
>>> dis.dis(hello)  
2           0 LOAD_GLOBAL              0 (print)
            2 LOAD_CONST               1 ('Hellog')
            4 CALL_FUNCTION            1
```   

<br/>  

> CPython 인터프리터 실행 중에 단점이 있는데 GIL(global interpreter lock)을 사용한다는 것이다. bytecode를 실행할 때에 여러 thread를 사용할 경우, 전체에 lock 을 걸어서 한번에 단 하나의 thread 만이 python object에 접근하도록 제한한 것이다. 하지만 single thread일 때는 문제가 없고 GIL의 단점을 보안하기 위한 방법들이 존재하고 있어서 GIL로 인한 불편함을 느낄 가능성은 거의 없다고 한다.  

<br/>  

### 그 외의 구현체

#### Jython
python 코드를 java 바이트코드로 만들어 JVM에서 실행할 수 있도록 한다. `.py` 파일을 `.class`파일로 컴파일 한다.  

#### PyPy
python 자체로 구현. JIT 컴파일 도입하여 CPython 보다 빠르다.

> JIT(just-in-time) 컴파일 이란 프로그램을 실행하기 전에 컴파일 하는 대신, 프로그램을 실행하는 시점에서 피료한 부분을 즉석으로 컴파일하는 방식. 보통 인터프리터 언어의 성능 향상을 목적으로 도입하는 경우가 많다. 인터프리트 하면서 자주 쓰이는 코드를 캐싱하기 때문에 인터프리터의 느린 실행 속도를 개선할 수 있다. JVM에서도 바이트코드를 기계어로 번역할 때 JIT 컴파일러를 사용한다.   

#### IronPython, Cython, WinPython ...

<br/>  

## Python의 장점
- 배우기 쉬워서 학습용으로 좋다.  
- 공동 작업과 유지 보수가 아주 쉽고 편해서 생산성이 높고 실사용률도 높다.  
- 읽고 쓰기 쉽다.
> 문법이 엄격한 것이 한 몫을 한다. python 에서는 중괄호를 들여쓰기를 강제하는 것으로 대체한다. 이 때문에 보기에도 편하고 중괄호를 닫지 않아서 발생하는 귀찮은 에러를 겪을 필요가 없다.        
들여쓰기에 탭(Tab)을 사용하면 안된다. 이유는 python interpreter가 tab을 공백 하나로 처리하기 때문이다. PEP-8에는 스페이스바 공백 4문자를 권장한다. 하지만 대부분의 에디터에서는 Tab을 공백 4개로 자동 변환해주는 기능을 제공하기 때문에 불편함 없이 사용할 수 있다.  

- 동적 타입으로 인해 유연하다.  
- C라이브러리 함수 call이 굉장이 쉽다.

<br/>  

## 정적 & 동적 타입  

### 정적 타입  
타입(자료형)을 지정해야한다. 컴파일 시에 자료형이 맞지않으면 컴파일 에러가 발생한다.  
타입 에러를 런타임이 아니라 컴파일 시에 발견할 수 있어서 보다 안전하다.
컴파일 시에 타입에 대한 정보를 결정하기 때문에 속도가 빠르다.
C, C++, java, ..  

_java_
```java
int a = 5;
boolean isNum = true;
String str = new String("string");
```    

### 동적 타입
Python은 동적타입을 사용한다.  
타입을 지정하지 않고 자유롭게 사용하여 편리하게 개발할 수 있다. 빠르게 개발할 수 있다.
개발을 하다보면 형변환 문제 때문에 귀찮을 때가 있다. 프로그래머들에게 형 변환이란 짜증나는 존재인데 이 문제를 신경쓰지 않을 수 있다. 따라서 빠르게 개발할 수 있다.  
Python, Ruby, SmallTalk, JavaScript, ..

단점은 느리고 런타임 시에 TypeError 가 발생할 수 있다.

_python ex_
```python
a = 5
isNum = True
str = "string"
```

> 덕 타이핑(Duck typing)이란 만약 어떤 새가 오리처럼 걷고, 헤엄치고, 꽥꽥거리는 소리를 낸다면 나는 그 새를 오리라고 부를 것이다. 타입을 미리 정하는게 아니라 실행이 되었을 때 해당 Method들을 확인하여 타입을 정한다.   
#### 장점
- 타입에 대해 매우 자유롭다.
- 런타임 데이터를 기반으로 한 기능과 자료형을 창출하는 것
#### 단점
- 런타임 자료형 오류가 발생할 수 있다 런타임에서, 값은 예상치 못한 유형이 있을 수 있고, 그 자료형에 대한 무의미한 작업이 적용된다.
- 이런 오류가 프로그래밍 실수 구문에서 오랜 시간 후에 발생할 수 있다
- 데이터의 잘못된 자료형의 장소로 전달되는 구문은 작성하지 않아야 한다. 이것은 버그를 찾기 어려울 수도 있다.

출처 : Nesoy [덕 타이핑 이란?](https://ko.wikipedia.org/wiki/%EB%8D%95_%ED%83%80%EC%9D%B4%ED%95%91)    

<br/>  

## python이 느린 이유에 대한 자료   

- [Why python is slow](http://jakevdp.github.io/blog/2014/05/09/why-python-is-slow/)  