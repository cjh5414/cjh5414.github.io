---
layout: post
title:  Ubuntu에서 Nginx, uWSGI를 이용하여 Django 배포하기
tags:   ['Django', 'Nginx', 'Linux']
---

> Ubuntu에서 Nginx, uWSGI를 이용하여 Django 프로젝트를 배포하는 방법에 대해서 알아본다. 로컬에서 개발할 때는 Django에서 제공하는 간단한 웹서버인 runserver를 이용하여 테스트를 했었다. 하지만 실제로 배포할 때는 사용하면 안되고 Nginx, Apache 등의 웹 서버를 이용하고 그 웹 서버와 Django Application 을 이어주는 Gunicorn, uWSGI 등을 이용하여 배포해야한다.  

<br/>  

## pyenv & pyenv-virtualenv  

Python을 사용하면 대부분 가상환경을 이용하는데 서버에서도 마찬가지로 설정을 해줘야한다.  

먼저 Ubuntu에서 Build 할 때 공통적으로 발생하는 문제를 방지하기 위해 [pyenv - Common build problems](https://github.com/pyenv/pyenv/wiki/Common-build-problems)을 참고하여 패키지들을 설치해준다.  

```
$ sudo apt-get install -y make build-essential libssl-dev zlib1g-dev libbz2-dev \
libreadline-dev libsqlite3-dev wget curl llvm libncurses5-dev libncursesw5-dev \
xz-utils tk-dev
```  

설치 및 설정 방법은 [Python 개발 환경 구축하기 - pyenv, virtualenv, autoenv](https://cjh5414.github.io/python-%EA%B0%9C%EB%B0%9C%ED%99%98%EA%B2%BD%EA%B5%AC%EC%B6%95/) 를 참고하면 된다.  

<br/>

## Nginx  

Nginx는 Apache와 같은 웹 서버로 더 가볍고 강력한 성능을 목표로 한 프로그램이다. Nginx와 Apache가 비교가 많이 되는데 서비스를 새로 시작하는 단계에서는 Nginx를 많이 선택하는 추세이다. 최근 Nginx 점유율이 급속히 증가하고 있다. 각각의 특징을 간단히 살펴보면 Apache는 MPM(Multi-Processing Modules) 방식으로 요청이 발생 했을 때 자식 프로세스를 생성하여 처리한다. Nginx는 Event-driven, 비동기(Asynchronous) 방식으로 Worker 프로세스 내부 listener가 요청을 받아 전달해준다. 따라서 Nginx는 프로세스와 쓰레드를 복제하지 않아서 부하가 증가하더라도 CPU/메모리 사용량이 크게 증가하지 않는다고 한다.  

Nginx의 Asynchronous, Event-driven 방식을 Apache의 Thread 기반 방식과 비교하여 이해하기 쉽게 설명한 포스트가 있다.  

- <http://sarc.io/index.php/nginx/76-2014-03-03-14-54-04>   

<br/>  

### package를 통한 nginx 설치  

Nginx를 설치하는 방법에는


```
$ sudo apt-get install nginx
```  

```
$ nginx -t
```
