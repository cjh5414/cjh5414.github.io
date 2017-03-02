---
layout: post
title:  Django settings.py 환경을 여러 개의 설정 파일로 분리하여 사용하기
tags:   [Python, Django]
---

> Django는 settings.py라는 모듈을 제공하여 환경설정을 하도록 한다. `$ django-admin startproject [myproject]` 으로 Django 프로젝트를 생성하면 _[myproject]/[myproject]/settings.py_ 파일이 생성되는데 대부분의 환경설정이 기본값으로 적용되어있다. 그 중에 `DEBUG = TRUE`로 기본 설정 돼 있는 변수가 있는데 개발 환경에서는 `TRUE`로, 실제 운영 환경에서는 `FALSE`로 수정하여 사용하곤 한다. 하나의 settings.py 파일만을 사용하지 않고 공통되는 객체 하나를 상속받아서 개발 환경, 테스트 환경, 운영 환경 등에 따라서 서로 다른 세팅 파일로 나누고 버전 컨트롤 시스템에서 관리하는 방법이 있다.  

<br/>  

## 환경을 분리하여 사용하는 이유  

Django 프로젝트의 환경을 분리하여 사용하면 `if DEBUG` 또는 `if not DEBUG` 등을 사용하지 않을 수 있고 반복되는 코드(설정)들을 없앨 수 있다. 개인적으로 사용하는 local 환경을 버전 컨트롤로 관리할 수 있고 필요한 경우 팀원들 간에 공유 할 수도 있다.   

<br/>  

## _settings.py_ 파일 분리하기  

```
_myproject_/
    _myproject_/
        settings/
            __init__.py
            base.py
            development.py
            production.py
```  

_settings.py_ 대신에 위와 같은 구조로 파일을 생성한다. 공통으로 사용되는 환경들을 _base.py_ 에 관리할 것이므로 우선 _settings.py_ 의 내용을 그대로 _base.py_ 에 복사한다. 이제 각각의 환경마다 다르게 사용될 설정들은 _base.py_ 에서 제거하고 환경에 따라 적절하게 작성한다. _base.py_ 를 import 해서 공통으로 사용되는 설정들을 상속받는다.  

_development.py_   

```python
from .base import *

DEBUG = True

DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': os.path.join(BASE_DIR, 'db.sqlite3'),
    }
}
```  

_production.py_  

```python
from .base import *

DEBUG = False

ALLOWED_HOSTS = ['mywebsite.com']

DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'name',
        'USER': 'user',
        'PASSWORD': 'password'
    }
}
```   

Django project을 생성할 때 _settings.py_ 에 기본으로 설정되는 `BASE_DIR`이라는 값이 있다. 프로젝트의 root path를 저장해두는 것으로 DB파일이 저장되는 위치를 지정하는 등의 파일 경로를 지정할 때 사용된다. root path를 구하는 과정을 살펴보면 현재 실행되는 파일의 절대경로를 기준으로 root 폴더까지의 디렉토리의 수 만큼 `os.path.dirname()`을 사용하여 구하는 방식인데, 우리가 사용하는 설정 파일은 기본적으로 생성되는 _settings.py_ 의 위치보다 한 단계 더 하위에 존재하므로 아래와 같이 `os.path.dirname()`을 한 번 더 입력해서 수정해줄 필요가 있다.  

_base.py_  

```python
import os

BASE_DIR = os.path.dirname(os.path.dirname(os.path.dirname(os.path.abspath(__file__))))

[...]
```  

<br/>  

## settings 적용  

이렇게 각각 나눠진 _development.py_ 와 _production.py_ 의 설정을 이용하여 shell과 runserver 명령을 이용하기 위해서는 `--settings` 옵션을 이용하면 된다.  

- `$ python manage.py shell --settings=myproject.settings.development`  
- `$ python manage.py runserver --settings=myproject.settings.development`  

하지만 매번 `--settings` 옵션을 입력하는 것은 매우 번거로운 일이다.  

`DJANGO_SETTINGS_MODULE` 환경변수를 원하는 환경으로 설정하면 `--settings` 옵션 없이도 이용할 수 있다.  

```
$ export DJANGO_SETTINGS_MODULE=myproject.settings.development
$ python manage.py runserver
```   

이것 또한 shell을 실행시킬 때마다 환경변수를 설정해줘야 하므로 번거롭다. python 가상환경을 사용하고 있다면 환경을 실행할 때 `DJANGO_SETTINGS_MODULE` 환경변수를 등록하도록 설정해주면 편하다. 여기서는 autoenv를 사용할 때, _.env_ 파일을 사용하여 설정하는 방법을 이용했다. (참고 : [python 가상 환경 구축하기](https://cjh5414.github.io/pytest-django-tutorial/))   

_.env_  

```
pyenv activate myproject-env
DJANGO_SETTINGS_MODULE=myproject.settings.development
```   

shell 에서 _.env_ 파일이 있는 경로로 이동하면 위의 내용을 알아서 실행하는데, 우선 `myproject-env`라는 가상환경을 실행하고 `DJANGO_SETTINGS_MODULE` 환경변수를 등록한다.  


<br/>  

## pytest-django _pytest.ini_  

_pytest.ini_ 파일을 이용하여 pytest-django를 사용하고 있다면 `DJANGO_SETTINGS_MODULE`를 아래와 같이 수정해줘야 된다. (참고 : [Django에서 pytest 사용하기](https://cjh5414.github.io/pytest-django-tutorial/))  

_pytest.ini_  

```
[pytest]
DJANGO_SETTINGS_MODULE=myproject.settings.development
python_files = tests.py test_*.py *_tests.py *_test.py
```   

<br/>  

## django 환경에 맞게 requirements.txt 분리

어떤 패키지는 운영 시에는 사용되지 않지만 개발의 편의를 위해서 필요한 경우가 있다. 이런 경우 `requirements.txt`와 `requirements-dev.txt` 등과 같이 패키지 리스트를 분리하여 사용하기도 한다. Django 환경을 분리하여 사용하는 경우 `requirements.txt` 파일들도 각각의 환경에 맞게 분리하여 사용하는 것이 좋다. 위에서 `settings/` 의 구조와 마찬가지로 `requirements/`을 아래와 같이 분리한다.   

```
requirements/
    base.txt
    development.txt
    production.txt
```  

마찬가지로 공통으로 사용되는 의존성은 _base.txt_ 에 넣고 따로 사용되는 의존성은 분리하여 각각의 파일에 넣는다.  

_base.txt_  

```
Django==1.10.5
[...]
```   

_development.txt_  

```
-r base.txt
pytest==3.0.5
[...]
```  

<br/>  

## 참고자료   

- Two Scoops of Django - 대니얼 로이 그린펠드/오드리 로이 그랜펠드 지음 | 김승진 옮김  

- <https://docs.djangoproject.com/en/1.10/topics/settings/>  
