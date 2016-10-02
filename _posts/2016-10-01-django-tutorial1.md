---
title: Django tutorial 1
layout: post
---

[Django 홈페이지](https://docs.djangoproject.com/en/1.10/intro/tutorial01/)에 있는 튜토리얼을 따라해보면서 장고에 대한 공부를 진행할 계획이다.  
이 튜토리얼에서는 투표 어플리케이션을 만드는 과정을 보여준다. python 버전은 3.5.2, django 버전은 1.10을 사용하고 가상 환경을 설정하여 진행한다.
python 개발환경에 관한 내용은 [여기](https://cjh5414.github.io/python-%EA%B0%9C%EB%B0%9C%ED%99%98%EA%B2%BD%EA%B5%AC%EC%B6%95/)를 참고하면 된다. 또한 진행중인 튜토리얼은 

## Install & setting


```
# 가상 환경 설정
$ pyenv virtualenv 3.5.2 django_tutorial
$ echo "pyenv activate django_tutorial" > .env
$ pyenv activate django_tutorial

# Django Install
$ pip install django==1.10
```
