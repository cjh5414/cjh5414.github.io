---
title: "Django tutorial 1"
layout: post
date:   2016-10-01 03:00:08
categories: django
comments: true
---

[Django 홈페이지](https://docs.djangoproject.com/en/1.10/intro/tutorial01/)에 있는 튜토리얼을 따라해보면서 장고에 대한 공부를 진행할 계획이다. python 버전은 3.5.2, django 버전은 1.10을 사용하고 가상 환경을 설정하여 진행한다. python 개발환경에 관한 내용은 [여기](https://cjh5414.github.io/python-%EA%B0%9C%EB%B0%9C%ED%99%98%EA%B2%BD%EA%B5%AC%EC%B6%95/)를 참고하면 된다. 또한 진행중인 튜토리얼은 [github](https://github.com/cjh5414/django_tutorial)으로 관리한다.  

이 튜토리얼에서는 기초적인 투표 어플리케이션을 만드는 과정을 보여준다.  

- 공개된 사이트로 사용자들이 투표 내용을 볼 수 있고, 투표에 참여할 수도 있다.  
- 관리자 사이트로 투표를 추가, 변경, 삭제 할 수 있다.  

## Install & setting  

```
# 프로젝트 폴더 생성
$ mkdir django_tutorial
$ cd django_tutorial

# 가상 환경 설정
$ pyenv virtualenv 3.5.2 django_tutorial
$ echo "pyenv activate django_tutorial" > .env
$ pyenv activate django_tutorial

# Django 설치
$ pip install django==1.10

# python, django version 확인
$ python --version
Python 3.5.2
$ python -m django --version
1.10

# git 설정
$ git init
$ echo ".env" >> .gitignore
$ git remote add origin https://github.com/username/repository.git
$ git add .
$ git status
$ git commit -m "django_tutorial project repository 생성"
$ git push origin master
```  


## Creating a project
