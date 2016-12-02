---
layout: post
title:  "python decorator 만들기 ( 작성중 )"
date:   2016-11-30
categories: python
---

<br>  

> TDD, Django 기반의 프로젝트를 진행하면서 Functional test 시작 부분에 자주 사용되는 코드를 decorator를 이용하여 구현해보았다. 기본적인 decorator를 구현하는 방법에 대해서 먼저 알아보았고 decorator 함수 내에서 decorator가 지정될 함수의 self를 이용하는 방법에 대해서도 알아보았다.  

<br>  

## python decorator ('@')  

python에 decorator라는 기능이 있다. 함수를 꾸며준다는 의미로 함수 정의 부분 위에 '@'기호로 시작하는 문장을 추가하여 사용한다. 함수의 앞뒤에 추가적으로 넣고 싶은 코드를 한 문장으로 지정하여 쉽게 재사용할 수 있도록 해주는 편리한 기능이다. 여러 함수들의 시작, 종료에 공통적으로 사용되는 코드가 있는 경우 decorator를 만들어서 사용할 수 있다.  

<br>  

## 함수 기반의 decorator  

아래와 같은 함수 두개가 있다.  

```python
def hi_function():
    print('함수 시작')
    print('hi')
    print('함수 끝')

def hello_function():
    print('함수 시작')
    print('hello')
    print('함수 끝')
```  

각각 함수를 호출해서 결과를 확인해보면 아래와 같은 메세지가 출력된다.  

```
함수 시작
hi
함수 끝
함수 시작
hello
함수 끝
```  

<br>  

### 함수를 이용한 decorator  

중복되는 부분인 `print('함수 시작')`, `print('함수 끝')`를 decorator 함수로 만들어서 사용해 보려고 한다.  

```python
def decorator_exam(func):
    def decorator_func():
        print('함수 시작')
        func()
        print('함수 끝')
    return decorator_func()

@decorator_exam
def hi_function():
    print('hi')

@decorator_exam
def hello_function():
    print('hello')
```  

두 함수를 호출해서 확인해보면 decorator를 적용하기 전의 결과와 동일하다.

<br>  

### class를 이용한 decorator  

```python
class DecoratorExam:
    def __init__(self, func):
        self.func = func

    def __call__(self, *args, **kwargs):
        print('함수 시작')
        self.func(*args, **kwargs)
        print('함수 끝')
```  
