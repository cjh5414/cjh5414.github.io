---
layout: post
title:  Django REST framework 시작하기
tags:   [Django]
---

> Django에는 REST API를 만들 수 있는 `Django REST framework`가 존재한다. [Django REST framework Quickstart](http://www.django-rest-framework.org/tutorial/quickstart/) 을 참고하여 tutorial을 진행해봤다. `Quickstart`에서는 django tutorial과 다른 디렉토리 구조를 사용하고 기본적으로 내장 되어있는 User, Group 에 대한 예제를 다룬다. 이와 다르게 django tutorial에서 사용하는 디렉토리 구조와 myapp이라는 앱을 생성하여 person 모델에 대한 api를 만드는 실습을 진행했다.   

<br/>  

## 프로젝트 설정  

_django-rest-tutorial_ 이라는 Root 디렉토리를 생성한다.  

```
$ mkdir django-rest-tutorial
$ cd django-rest-tutorial
```  

python 3.5.2 버전으로 가상환경을 구축한다.  

```
$ pyenv install 3.5.2
$ pyenv virtualenv 3.5.2 django-rest-tutorial
$ pyenv activate django-rest-tutorial
```  

`django` 와 `djangorestframework`를 pip로 설치한다.  

```
$ pip install django
$ pip install djangorestframework
```  

`tutorial`라는 이름의 Django project를 생성하고, `myapp`이라는 이름의 앱을 생성한다.  

```
$ django-admin.py startproject tutorial
$ cd tutorial
$ python manage.py startapp myapp
```  

Root 디렉토리의 프로젝트 구조를 보면 아래와 같다.   

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

<br/>  

## Settings  

_settings.py_  

```python
INSTALLED_APPS = [
    ...
    'rest_framework',
    'myapp'
]
```  

<br/>  

## Models  

_myapp/models.py_  

```python
from django.db import models


class Person(models.Model):
    first_name = models.CharField(max_length=30)
    last_name = models.CharField(max_length=30)
```  

<br/>  

## Serializers  

_myapp/serializers.py_  

```python
from myapp.models import Person
from rest_framework import serializers


class PersonSerialier(serializers.HyperlinkedModelSerializer):
    class Meta:
        model = Person
        fields = ('first_name', 'last_name')
```  

<br/>  

## Views  

_myapp/views.py_  

```python
from rest_framework import viewsets
from serializers import PersonSerialier
from myapp.models import Person


class PersonViewSet(viewsets.ModelViewSet):
    queryset = Person.objects.all()
    serializer_class = PersonSerialier
```  

<br/>  

## URLs  

_urls.py_  

```python
from django.conf.urls import url, include
from rest_framework import routers
from myapp import views


router = routers.DefaultRouter()
router.register(r'persons', views.PersonViewSet)

urlpatterns = [
    url(r'^', include(router.urls)),
    url(r'^api-auth/', include('rest_framework.urls', namespace='rest_framework'))
]
```   

<br/>  

## Run

```
$ python manage.py makemigrations
$ python manage.py migrate
$ python manage.py runserver
```  

![result home](/images/django-rest-framework/result home.png)  
![result persons](/images/django-rest-framework/result persons.png)  
