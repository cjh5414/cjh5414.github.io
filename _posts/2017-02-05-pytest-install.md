---
layout: post
title:  pytest - Installation and Getting Started
tags:   [Python, Test, pytest]
---

> Python Django를 이용하고 TDD를 적용하는 프로젝트를 시작하기에 앞서 pytest에 대해 공부해보려고 한다. [pytest Documentation](https://media.readthedocs.org/pdf/pytest/latest/pytest.pdf)을 읽어보면서 하나씩 따라 해보고 공부한 내용들을 블로그에 남길 예정이다. (버전은 Python은 3.6.0 pytest는 3.0.6을 사용한다.)  
이번 포스트는 pytest 설치와 기본 설정에 대한 내용이다.


<br/>  

## Settings  

예제로 사용되는 코드는 Git으로 관리하고 Github에 올릴 예정이다.  

- [pytest Documentation repository](https://github.com/cjh5414/pytest-Documentation)   

<br/>  

pyenv, virtualenv, autoenv를 이용하여 python 개발환경을 구축하였다. python 버전은 3.6.0을 설치했다.  

![pyenv](/images/pytest-installation/pyenv.png)  

python 개발환경 구축에 대한 자료는 아래의 링크를 참고하면 된다.  

- [Python 개발 환경 구축하기 - pyenv, virtualenv, autoenv](https://cjh5414.github.io/python-%EA%B0%9C%EB%B0%9C%ED%99%98%EA%B2%BD%EA%B5%AC%EC%B6%95/)  

<br/>  

## Installation   

```
$ pip install -U pytest

$ pytest --version
This is pytest version 3.0.6, imported from /Users/jihun/.pyenv/versions/3.6.0/envs/pytest-Documentation/lib/python3.6/site-packages/pytest.py
```  

패키지를 설치할 때 `-U` 옵션을 주는 이유는 이미 낮은 버전의 패키지가 설치돼 있는 경우 최신 버전으로 업그레이드를 시켜준다.  

<br/>  

## Our first test run  

_test_sample.py_  

```python
def func(x):
    return x + 1

def test_answer():
    assert func(3) == 5
```  

`func()` 라는 함수를 정의하고 이 함수를 테스트하는 test_answer()를 정의하였다.  
`test_answer()`는 func(3) 값이 5인지 확인하고 있지만 실제 값은 4이므로

`$ pytest` 명령으로 테스트를 돌려보면 아래와 같이 실패하는 결과가 나온다.  

![test_sample fail](/images/pytest-installation/test_sample fail.png)  

> pytest 명령을 실행하면 현재 디렉토리와 하위 디렉토리에서 test로 시작하거나 끝나는 파일(test_*.py, *_test.py)을 모두 찾아서 실행한다. 실행된 파일 안에 있는 함수 중에서 test로 시작하는 함수만 찾아서 테스트를 수행한다.   

<br/>  

## Asserting that a certain exception is raised   

exception을 제대로 발생시키는지 확인하기 위한 테스트는 아래와 같이 pytest의 _raises_ helper함수를 이용한다.  

_test_sysexit.py_  

```python
import pytest
def f():
    raise SystemExit(1)

def test_mytest():
    with pytest.raises(SystemExit):
        f()
```  

`-q` 옵션은 결과를 더 간략히 보여준다.  

```
$ pytest -q test_sysexit.py
.
1 passed in 0.00 seconds
```  

<br/>  

## Grouping multiple tests in a class  

테스트가 많아지기 시작하면 테스트들을 논리적으로 클래스와 모듈로 그룹화 하는 것이 좋다. 한 클래스안에 두 가지 테스트를 작성한 예제를 보자.  

_test_class.py_  

```python
class TestClass:
    def test_one(self):
        x = "this"
        assert 'h' in x

    def test_two(self):
        x = "hello"
        assert hasattr(x, 'check')
```  

> hasattr(object, name) 함수는 python 내장 함수로 Object 내에 name에 해당하는 attribute가 있으면 True, 없으면 False를 반환한다.  

위에서도 언급했듯이 _test_class.py_ 파일만 실행시켜주면 TestClass의 두 테스트 함수를 찾아서 테스트해준다.  

![test_class result](/images/pytest-installation/test_class result.png)  

첫 번째 테스트는 통과하고 두 번째 테스트는 실패했다. 실패한 위치와 assertion에 사용된 값들을 비교하여 보여주기 때문에 실패의 원인을 쉽게 파악할 수 있다.  

<br/>  

## Going functional: requesting a unique temporary directory  

기능 테스트에서 종종 어떤 파일을 생성하고 어플리케이션 객체로 전달해야할 필요가 있다. pytest는 내장된 fixtures/function 인자를 통해 임시의 파일과 같은 자원을 요청하여 사용할 수 있도록 해준다.

_test_tmpdir.py_  

```python
def test_needsfiles(tmpdir):
    print (tmpdir)
    assert 0
```  

test함수에 `tmpdir`이라는 인자를 입력하면 pytest가 테스트 함수를 실행하기 전에 fixture factory를 호출해서 자원을 생성한다.  

![test_tmpdir result](/images/pytest-installation/test_tmpdir result.png)  

테스트를 실행하기 전에, 테스트를 위한 임시 폴더가 생성된다.   

pytest의 내장된 fixtures 의 종류는 아래의 명령으로 찾아볼 수 있다.  

```
pytest --fixtures
```

<br/>  

## 참고자료  

<https://media.readthedocs.org/pdf/pytest/latest/pytest.pdf>  
