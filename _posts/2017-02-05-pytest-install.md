---
layout: post
title:  pytest - Installation and Getting Started
tags:   [Python, Test, pytest]
---

> Python Django를 이용하고 TDD를 적용하는 프로젝트를 시작하기에 앞서 pytest에 대해 공부해보려고 한다. [pytest Documentation](https://media.readthedocs.org/pdf/pytest/latest/pytest.pdf)을 읽어보면서 하나씩 따라 해보고 공부한 내용들을 블로그에 남길 예정이다.
이번 포스트는 pytest 설치와 기본 설정에 대한 내용이다. 버전은 Python은 3.6.0 pytest는 3.0.6을 사용한다.  


<br/>  

## Settings  

예제로 사용되는 코드는 Git으로 관리하고 Github에 저장할 예정이다.  
- [pytest Documentation repository](https://github.com/cjh5414/pytest-Documentation)   

pyenv, virtualenv를 이용하여 python 개발환경을 구축하였다. python 버전은 3.6.0을 설치했다.  

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

```
# content of test_sample.py
def func(x):
    return x + 1

def test_answer():
    assert func(3) == 5
```  

`func()` 라는 함수를 정의하고 이 함수를 테스트하는 test_answer()를 정의하였다.  
`test_answer()`는 func(3) 값이 5인지 확인하고 있지만 실제 값은 4이므로

`$ pytest` 명령으로 테스트를 돌려보면 아래와 같이 실패하는 결과가 나온다.  

![test_sample fail](/images/pytest-installation/test_sample.png)  

> pytest 명령을 실행하면 현재 디렉토리와 하위 디렉토리에서 test로 시작하거나 끝나는 파일(test_*.py, *_test.py)을 모두 찾아서 실행한다. 파일 안에 있는 함수 중에서 test로 시작하는 함수만 찾아서 테스트를 수행한다.   

<br/>  

## 참고자료  

<https://media.readthedocs.org/pdf/pytest/latest/pytest.pdf>  
