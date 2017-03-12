---
layout: post
title:  Django 예제를 이용한 python import 이해하기
tags:   [Python, Django]
---

> [이전 포스트](https://cjh5414.github.io/django-rest-framework/)에서 질문 받았던 Django의 python import에 대해 해답을 주기 위해 공부한 내용을 정리했다.  

<br/>  

## Example  

아래와 같은 구조의 django 프로젝트가 있고 _tutorial/tutorial/urls.py_ 에서 _tutorial/myapp/views.py_ 를 import 하는 내용이다.  

```
$ tree ../../django-rest-tutorial/
django-rest-tutorial/
└── tutorial
    ├── manage.py
    ├── myapp
    │   ├── __init__.py
    │   ├── admin.py
    │   ├── apps.py
    │   ├── migrations
    │   │   └── __init__.py
    │   ├── models.py
    │   ├── tests.py
    │   └── views.py
    └── tutorial
        ├── __init__.py
        ├── settings.py
        ├── urls.py
        └── wsgi.py
```  

_tutorial/tutorial/urls.py_   

```
[...]

from myapp import views

[...]
```  

<br/>  

## python import 원리  

python에서 모듈을 절대 import 하면 그 모듈을 찾기 위해 아래와 같은 sys.path의 경로들을 탐색한다.

- 실행된 모듈의 경로

- 환경변수 PYTHONPATH에 지정된 경로  

- Python이 설치된 경로 및 라이브러리의 경로  

위의 예제에서는 직접 구현한 모듈을 import 하는 것이고, manage.py 모듈을 통해 실행하므로 manage.py가 위치한 경로에서 탐색한다.  

<br/>

## import path

위의 설명을 덧붙이면

_tutorial/tutorial/urls.py_ 에 아래의 코드를 추가하여  

```
import sys
print(sys.path)
```  

manage.py를 통해 실행시켜보면,

```
$ python manage.py runserver
['.../django-rest-tutorial/tutorial', ...]
```  

 현재 모듈이 실행된 `sys.path`가 `.../django-rest-tutorial/tutorial`인 것을 확인할 수 있다. `import myapp import views`를 할 경우 이 `sys.path`의 경로를 기반으로 `.../django-rest-tutorial/tutorial/myapp` 와 같이 `myapp` 모듈을 찾아서 views를 import 하게 된다.  
