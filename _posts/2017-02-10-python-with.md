---
layout: post
title:  Python with 구문과 context manager 이해하기
tags:   [Python]
---

> pytest doc의 [The writing and reporting of assertions in tests](https://cjh5414.github.io/pytest-assertion/) 을 읽어보던 중에 with 구문과 context manager에 대한 내용이 있었는데 개념을 잘 몰라서 정리해봤다. 관련 글인 [Understanding Python's "with" statement](http://effbot.org/zone/python-with-statement.htm)을 참고하여 작성했다.  

<br/>  

`with`은 파이썬 2.5에서 도입된 기능으로 context manager에 의해서 실행되는 `__enter__()`과 `__exit__()`을 정의하여, _with 구문 body_ 의 앞부분과 뒷부분에 실행되는 코드를 대신할 수 있다.   
with 구문을 이용하면 try/finally을 대신하여 더 간편하고 쉽게 사용할 수 있다.  

아래의 예제 코드를 보자.  

```python
set things up
try:
    do something
finally:
    tear things down
```  

`set things up`에는 file을 열거나 외부 리소스와 같은 것을 얻는 처리가 해당되고, `tear things down`에는 file을 닫거나 리소스를 제거, 해제 하는 처리가 해당된다. 이와 같은 `try-finally` 구조는 코드가 제대로 동작하지 않고 끝나더라도 `tear things down`은 무조건 실행되는 것을 보장한다.  

이런 코드는 많이 사용되는 것으로, 아래와 같이 `set things up`과 `tear things down` 부분을 `__enter__()`과 `__exit__()`을 정의하여 사용한다면 재사용성이 높아지고 편리할 것이다.  

```python
class controlled_execution:
    def __enter__(self):
        set things up
        return thing
    def __exit__(self, type, value, traceback):
        tear things down

with controlled_execution() as thing:
     some code using thing
```  

`with` 구문이 실행되면, context manager에 의해서 `__enter__`이 실행되고 여기서 반환하는 값이 `as`의 thing로 지정된다. 그 후에 `some code using thing`에 해당하는 body code를 실행하고, _코드에 무슨 일이 있다 하더라도_ 마지막에 `__exit__`은 호출이 보장된다.  

python의 `file` 객체는 `__enter__`와 `__exit__` 함수가 구현되어있다. 전자는 file object 객체 자신을 리턴하고, 후자는 file을 close 한다.  

```
>>> f = open("x.txt")
>>> f
<open file 'x.txt', mode 'r' at 0x00AE82F0>
>>> f.__enter__()
<open file 'x.txt', mode 'r' at 0x00AE82F0>
>>> f.read(1)
'X'
>>> f.__exit__(None, None, None)
>>> f.read(1)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
ValueError: I/O operation on closed file
```   

따라서 file을 열고, 사용한 후에 닫는 것을 보장하도록 하는 것을 아래처럼 매우 간단하게 사용할 수 있다.  

```python
with open("x.txt") as f:
    data = f.read()
    do something
```  

<br/>  

## 참고자료  

<https://docs.python.org/3/reference/datamodel.html#context-managers>  
<http://effbot.org/zone/python-with-statement.htm>  
