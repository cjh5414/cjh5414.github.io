---
layout: post
title:  Python doctest
date:   2017-01-24
tags:   [Python]
---

<br/>  

> python 표준 라이브러리 중에 `doctest` 라는 것이 존재한다. documentation string(docstring) 안에 포함된 python 명령을 실행하고 예상 결과를 확인함으로써 코드를 테스트 할 수 있다. 사용법이 매우 간단하며 docstring에 예상 결과 코드를 넣는 것으로 함수의 설명을 대신하는 것과 동시에 코드를 테스트할 수도 있는 장점이 있다. 그러나 코드를 테스트함에 있어서는 코드가 복잡 해짐에 따라 `doctest` 보다는 `unittest` 를 사용하는 것이 더 체계적이고 정확할 것이다.  

<br/>  

## Simple example  

아래와 같은 코드를 넣어서 _doctest_simple.py_ 라는 파일을 생성한다. `my_function(a, b)` 은 a와 b의 곱을 반환하는 함수이고 실행 했을 때 예상되는 결과에 대한 `2*3`, `'a'*3` 두 가지 경우를 docstring에 작성한다.  

_doctest_simple.py_  

```python
def my_function(a, b):
    """Returns a * b.

    Works with numbers:

    >>> my_function(2, 3)
    6

    and strings:

    >>> my_function('a', 3)
    'aaa'
    """
    return a * b
```  

<br/>  

### Run  

작성한 코드를 doctest로 실행해보기 위해 아래와 같은 명령어로 실행한다.  
`-m` 옵션으로 doctest를 메인 모듈로 설정하고,  
`-v` 옵션으로 테스트 실행 중에 결과를 출력해주도록 설정을 바꿔준다.  
(옵션을 입력하지 않으면 결과를 출력해주지 않는다.)  

```
$ python -m doctest -v doctest_simple.py
```

<br/>  

### Result  

doctest 는 `>>>` 으로 시작하는 python 명령문을 찾아서 실행시키고 아래의 예측 값과 비교하여 결과를 알려준다. 이 때, docstring 에 작성된 `Returns a * b.`, `Works with numbers:`, `and strings:` 와 같은 text들은 doctest에 의해 무시되고 테스트 결과에 영향을 주지 않는다.  

```
Trying:
    my_function(2, 3)
Expecting:
    6
ok
Trying:
    my_function('a', 3)
Expecting:
    'aaa'
ok
1 items had no tests:
    doctest_simple
1 items passed all tests:
   2 tests in doctest_simple.my_function
2 tests in 2 items.
2 passed and 0 failed.
Test passed.
```  

<br/>  

### Wrong example  

예측 값과 결과 값이 일치하지 않으면 아래와 같은 메세지가 출력되면서 테스트가 실패한다.  

_doctest_simple.py_  

```python
def my_function(a, b):
    """Returns a * b.

    [...]

    >>> my_function(2, 3)
    5
    """
    return a * b
```  

```
Trying:
    my_function(2, 3)
Expecting:
    5
**********************************************************************
File "doctest_simple.py", line 16, in doctest_simple.my_function
Failed example:
    my_function(2, 3)
Expected:
    5
Got:
    6
1 items had no tests:
    doctest_simple
**********************************************************************
1 items had failures:
   1 of   3 in doctest_simple.my_function
3 tests in 2 items.
2 passed and 1 failed.
***Test Failed*** 1 failures.
```  

<br/>  

## Run tests by module  

위에서는 doctest를 하기 위해서 command line 에서 모듈을 실행할 때 doctest 명령을 직접 입력하여 실행했었는데, command line에서 모듈을 실행했을 때 doctest를 실행하도록 하는 방법도 있다. `testmod()` 를 사용하는 코드를 맨 아래에 다음과 같이 추가하면 된다.  

> command line에서 특정 모듈을 실행하는 경우 `__name__ == '__main__'` 을 만족하여  if문 아래의 문장이 실행된다.   

_doctest_simple.py_   

```python
def my_function(a, b):
    """
    >>> my_function(2, 3)
    6
    >>> my_function('a', 3)
    'aaa'
    """
    return a * b

if __name__ == '__main__':
    import doctest
    doctest.testmod()
```  

<br/>  

```
$ python doctest_testmod.py -v
```  

으로 실행 해보면,

```
Trying:
    my_function(2, 3)
Expecting:
    6
ok
[...]
```  

동일한 결과가 출력된다.  

<br/>  

### Split script running doctest  

`testmod()`에 인자로 모듈명을 넣을 수 있는데 이를 이용하면 여러개의 모듈을 가져와서 차례대로 테스트하는 별도의 테스트 스크립트를 만들 수 있다.  

_doctest_testmod_other_modules.py_  

```python
import doctest_simple
import doctest_simple2

if __name__ == '__main__':
    import doctest
    doctest.testmod(doctest_simple)
    doctest.testmod(doctest_simple2)
```  

```
$ python doctest_testmod_other_modules.py -v
Trying:
    my_function(2, 3)
Expecting:
    6
ok

[...]
```  

<br/>  

## Run doctest by file  

doctest의 내용을 별도의 파일로 분리해서 사용하는 방법도 있다.  

_doctest_my_function.py_  

```python
def my_function(a, b):
    return a * b
```  

_doctest_in_help.rst_  

```
>>> from doctest_my_function import my_function
>>> my_function(2, 3)
6
>>> my_function('a', 3)
'aaa'
```  

_doctest_testfile.py_  

```python
import doctest

if __name__ == '__main__':
    doctest.testfile('doctest_in_help.rst')
```  

<br/>  

### Run script & Result  

```
$ python doctest_testfile.py -v
Trying:
    from doctest_my_function import my_function
Expecting nothing
ok
Trying:
    my_function(2, 3)
Expecting:
    6
ok
Trying:
    my_function('a', 3)
Expecting:
    'aaa'
ok
1 items passed all tests:
   3 tests in doctest_in_help.rst
3 tests in 1 items.
3 passed and 0 failed.
Test passed.
```

<br/>  

## 참고자료  

<https://pymotw.com/2/doctest/>  
