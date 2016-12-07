---
layout: post
title:  "python decorator에 @wraps를 사용해야 하는 이유"
date:   2016-12-6
categories: python
---

<br>  

> python decorator를 구현하기 위해 구글링을 해보는 중에 decorator함수에 @wraps를 사용하는 것을 자주 볼 수 있었다. @wraps를 사용 하는 것과 사용 하지 않는 것의 결과는 차이가 없었는데 왜 사용하는지 이유가 궁금해서 찾아보았다. 결론부터 말하자면 decorator를 이용하면 debugging이 어려워 지는데 @wraps를 사용하면 그 문제를 해결할 수 있다.  

<br>  

여러 함수들의 시작, 종료에 공통적으로 사용되는 코드가 있는 경우 decorator를 만들어서 사용하면 매우 유용하다. ([python decorator 사용법](https://cjh5414.github.io/python-decorator/))  
하지만 decorator를 이용한 코드는 디버깅하기 어려운 단점이 있다.  

<br>  

### @wraps를 사용하지 않는 경우  

```python
from functools import wraps


def without_wraps(func):
    def __wrapper(*args, **kwargs):
        return func(*args, **kwargs)
    return __wrapper

@without_wraps
def my_func_a():
    """Here is my_func_a doc string text."""
    pass
```  

```python
# 아래는 @wraps가 없는 decorator를 이용한 함수의 결과이다.
print my_func_a.__doc__
>>> None
print my_func_a.__name__
>>> __wrapper
```  

위의 결과를 보면 알 수 있듯이 my_func_a()라는 함수의 name과 doc를 출력해보면 원하는 결과를 얻지 못하는 것을 확인할 수 있다.  

@without_wraps decorator를 지정하면 `__wrapper()`라는 함수가 `my_func_a()`를 감싸주기 때문에 name과 doc이 `__wrapper`로 출력되기 때문이다.  

<br>  

### @wraps를 사용하는 경우  

python에서는 이런 문제를 다루기 위해 @wraps라는 것을 제공한다.  

```python
from functools import wraps


def without_wraps(func):
   def __wrapper(*args, **kwargs):
       return func(*args, **kwargs)
   return __wrapper

@with_wraps
def my_func_b():
    """Here is my_func_b doc string text."""
    pass
```  

```python
# 아래는 @wraps가 있는 decorator를 이용한 함수의 결과이다.
print my_func_b.__doc__
>>> Here is my_func_b doc string text.
print my_func_b.__name__
>>> my_func_b
```  

@wraps를 지정하여 decorator를 구현하면 문제없이 해당 함수를 디버깅할 수 있다.  

<br>  

## 참고자료  

<https://artemrudenko.wordpress.com/2013/04/15/python-why-you-need-to-use-wraps-with-decorators/>
