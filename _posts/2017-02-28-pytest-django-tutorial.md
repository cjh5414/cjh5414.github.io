---
layout: post
title:  Django 에서 pytest 사용하기 - 'pytest와 pytest-django 시작하기' 번역
tags:   [Python, Test, pytest, Django]
---

> Django 프로젝트에서 unittest을 작성하고 pytest로 실행시키면 환경 설정이 안 돼 있다는 에러 메세지가 뜬다. Django에서 pytest를 이용하기 위한 pytest-django 모듈이 따로 존재한다. [Getting started with pytest and pytest-django](http://pytest-django.readthedocs.io/en/latest/tutorial.html)을 번역하고 적용해보았다.  

<br/>  

## 예시로 사용될 Django project  

예시로 사용될 Djangoe project의 구조이다. lists라는 앱을 생성했고, 앱을 생성할 때 자동으로 생성되는 _lists/tests.py_ 파일에 간단한 테스트를 작성했다.  

```
$ tree superlists/
superlists/
├── lists
│   ├── __init__.py
│   ├── admin.py
│   ├── apps.py
│   ├── migrations
│   │   ├── __init__.py
│   ├── models.py
│   ├── tests.py
│   └── views.py
├── manage.py
└── superlists
   ├── __init__.py
   ├── settings.py
   ├── urls.py
   └── wsgi.py
```

<br/>  

## 서론  

`pytest`와 `pytest-django`는 표준 장고 테스트 및 Nose 테스트와 호환된다. 이들은 간단하거나 거의 없는 환경설정으로 존재하는 테스트를 찾아서 실행할 수 있다. 빠르게 시작하는 방법에 대해 이 글에서 설명한다.  

<br/>

## 설치  

`pytest-django` 는 pip을 통해 설치할 수 있다.  

```
$ pip install pytest-django
```

`pytest-django`를 설치하면 자동적으로 최신버전의 `pytest`도 같이 설치된다. `pytest-django`는 `pytest`의 플러그인 시스템을 사용하고 특별한 설정 없이 설치 후에 바로 사용 가능하다.

<br/>  

설치한 후에 `pytest`을 실행해보면 _lists/tests.py_ 를 발견하지 못한다.  
`pytest lists/test.py` 명령으로 app의 테스트를 명시적으로 실행시켜보면 에러가 발생한다.  

```
E django.core.exceptions.ImproperlyConfigured: Requested settings, but settings are not configured.
You must either define the environment variable DJANGO_SETTINGS_MODULE or call settings.configure() before accessing settings.
```   

이후에 나오는 설정을 적용해야 정상적으로 동작한다.  

<br/>  

## Django 설정에 pytest 지정하기  

`pytest`에게 테스트를 실행하기 위한 Django 설정을 알려줄 필요가 있다. 가장 쉬운 방법은 이런 정보를 가진 `pytest` 환경설정 파일을 만드는 것이다.  

프로젝트 root directory에 _pytest.ini_ 라는 파일을 생성하고 아래의 내용을 작성한다.  

```
[pytest]
DJANGO_SETTINGS_MODULE = yourproject.settings
```   

_DJANGO_SETTINGS_MODULE_ 환경 변수를 설정하거나 커맨드 라인에서 테스트를 실행할 때 `--ds=yourproject.settings`옵션을 명시하는 것과 같이 Django setting을 할 수도 있다.  
여기에 대한 전체적인 내용은 [Configuring Django settings](http://pytest-django.readthedocs.io/en/latest/configuring_django.html#configuring-django-settings)을 참고하면 된다.  

또한 선택사항으로, `[pytest]` 섹션에 아래의 내용을 입력하면 `pytest`가 장고의 기본 app 레이아웃에 따라 test를 찾아낼 수 있다. 더 자세한 정보는 [My tests are not being found. Why?](http://pytest-django.readthedocs.io/en/latest/faq.html#faq-tests-not-being-picked-up)를 참고하자.    

```
python_files = tests.py test_*.py *_tests.py
```  

<br/>  

위의 내용을 바탕으로 _pytest.ini_ 파일을 _superlists/_ 에 생성하고 아래의 내용을 넣었다.  

```
$ tree superlists/ -L 1
superlists/
├── lists
├── manage.py
├── superlists
└── pytest.ini
```     

_pytest.ini_  

```
[pytest]
DJANGO_SETTINGS_MODULE = superlists.settings
python_files = tests.py test_*.py *_tests.py
```  


<br/>  

## 테스트 실행  

테스트를 실행하는 방법은 `manage.py test`을 사용하는 대신에 아래의 간단한 명령으로 가능하다.  

```
$ pytest
```   

<br/>  

`$ pytest lists/tests.py`으로도 테스트 실행이 가능하지만, `pytest` 뒤에 _lists/tests.py_ 테스트 파일을 지정하지 않아도 `$ pytest` 명령 하나로 lists앱의 테스트파일을 알아서 찾아서 실행시켜준다.  

<br/>  

## 추가자료  

- [왜 pytest를 사용해야 하는가?](https://cjh5414.github.io/why-pytest/)  
