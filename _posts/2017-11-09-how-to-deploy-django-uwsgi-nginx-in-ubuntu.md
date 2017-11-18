---
layout: post
title:  Ubuntu에서 Nginx, uWSGI를 이용하여 Django 배포하기
tags:   ['Django', 'Nginx', 'Linux']
---

> Ubuntu에서 Nginx, uWSGI를 이용하여 Django 프로젝트를 배포하는 방법에 대해서 알아본다. 로컬에서 개발할 때는 Django에서 제공하는 간단한 웹서버인 runserver를 이용하여 테스트를 했었다. 하지만 실제로 배포할 때는 그렇게 사용하면 안되고 Nginx, Apache 등의 웹 서버를 이용하고 그 웹 서버와 Django Application 을 이어주는 Gunicorn, uWSGI 등을 이용하여 배포해야한다. `Memorist` 라는 이름의 프로젝트를 예시로 사용했다.  

<br/>

## Nginx  

Nginx는 Apache와 같은 웹 서버로 더 가볍고 강력한 성능을 목표로 한 프로그램이다. Nginx와 Apache가 비교가 많이 되는데 서비스를 새로 시작하는 단계에서는 Nginx를 많이 선택하는 추세이다. 최근 Nginx 점유율이 급속히 증가하고 있다. 각각의 특징을 간단히 살펴보면 Apache는 MPM(Multi-Processing Modules) 방식으로 요청이 발생 했을 때 자식 프로세스를 생성하여 처리한다. Nginx는 Event-driven, 비동기(Asynchronous) 방식으로 Worker 프로세스 내부 listener가 요청을 받아 전달해준다. 따라서 Nginx는 프로세스와 쓰레드를 복제하지 않아서 부하가 증가하더라도 CPU/메모리 사용량이 크게 증가하지 않는다고 한다.  

Nginx의 Asynchronous, Event-driven 방식을 Apache의 Thread 기반 방식과 비교하여 이해하기 쉽게 설명한 포스트가 있다.  

- <http://sarc.io/index.php/nginx/76-2014-03-03-14-54-04>   

<br/>  

### package를 통한 nginx 설치  

package를 이용하여 nginx를 설치한다.  

```
$ sudo apt-get install nginx
```   

설치가 완료되면 아래와 같이 버전과 상태를 확인해 볼 수 있다. nginx는 설치가 완료되면 알아서 실행시키기 때문에 동작하고 있는 상태로 표시된다.    

```
$ nginx -v
nginx version: nginx/1.4.6 (Ubuntu)
$ sudo service nginx status
 * nginx is running
```  

웹 브라우저를 통해 서버에 접속해보면 아래와 같은 화면이 뜬다.  

![nginx default](/images/ubuntu-deploy-django/nginx default.png)   

<br/>  

## pyenv & pyenv-virtualenv & autoenv  

Python을 사용하면 대부분 가상환경을 이용하는데 서버에서도 마찬가지로 설정을 해줘야한다.  

설치 및 설정 방법은 [Ubuntu에서 pyenv, virtualenv, autoenv 이용하기](https://cjh5414.github.io/ubuntu-pyenv-virtualenv/) 를 참고하면 된다.  

<br/>  

## Nginx Server Block 설정  

Nginx 설정을 쉽게 정리해놓은 링크가 있다. 참고하여 nginx server 설정 및 도메인 설정 후에 계속 진행하는 것이 좋다.   

- [Nginx Server Blocks 설정](https://www.digitalocean.com/community/tutorials/how-to-set-up-nginx-server-blocks-virtual-hosts-on-ubuntu-14-04-lts)    

- [Django 배포하기 with Gunicorn, Nginx](https://cjh5414.github.io/nginx/)     

<br/>  

## 프로젝트 clone & Django 설정  

`$ git clone https://abc.git`

git clone 명령으로 프로젝트를 clone 받고 DB 설정을 위해 Migration을 진행한다.  

```
$ python manage.py makemigrations;
$ python manage.py migrate;
```  

### collectstatic  

웹 사이트는 일반적으로 image, js, css 등의 정적 파일들을 따로 관리한다. 아래의 명령으로 static 파일을 따로 분리하는 작업을 해준다.

```
$ python manage.py collectstatic
```   

참고로 Memorist는 django settings 파일의 설정에 따라 정적 파일들이 `/home/jihun/Memorist/memorist/staticfiles` 에 위치하도록 되어있다. 아래의 포스트에 Django Managing static files 에 관한 더 자세한 내용이 나와있다.  

- [Django Managing static files](https://docs.djangoproject.com/en/1.11/howto/static-files/)  

<br/>

## uWSGI 설치  

python 패키지 `uwsgi`를 설치한다.  

```
$ pip install uwsgi
```  

<br/>  

## uWSGI 설정  

_.ini_ 파일을 이용하여 uWSGI를 실행시키는 방법을 이용한다.  

우선 아래와 같이 _projectname_uwsgi.ini_ 파일을 작성한다.  

_memorist_uwsgi.ini_

```
[uwsgi]

chdir = /home/jihun/Memorist/memorist
module = memorist.wsgi

socket = /home/jihun/Memorist/memorist/memorist.sock
# socket = 127.0.0.1:8001
chmod-socket = 666
vacuum = true

home = /home/jihun/.pyenv/versions/Memorist/
virtualenv = /home/jihun/.pyenv/versions/Memorist/

daemonize = /home/jihun/Memorist/memorist/memorist_uwsgi.log

die-on-term = true
```  

- `module` : django 프로젝트를 생성하면 자동으로 생성되는 wsgi 파일을 지정한다.    
- `socket` : socket 파일을 이용하는 경우 소켓이 생성될 위치와 함께 .sock 파일명을 입력한다. ip, port를 이용하고 싶은 경우 주석으로 처리된 부분과 같이 지정하면 된다.   
- `home`, `virtualenv` : virtualenv를 이용하는 경우 가상 환경의 경로를 지정해줘야 한다.  
- `daemonize` : 백그라운드로 실행 하도록 하는 설정을 하면서 로그가 저장될 파일 위치를 지정한다.  

<br/>  

## uWSGI 실행  

위에서 작성한 _memorist_uwsgi.ini_ 파일을 이용하여 uWSGI를 실행시키는 방법이다.    

실행 전 파일 구조는 아래와 같은 상태이다.  

```
$ pwd
/home/jihun/Memorist/memorist

$ tree .
.
├── manage.py
├── memorist
│   ├── settings.py
│   ├── urls.py
│   └── wsgi.py
├── memorist_uwsgi.ini
├── staticfiles
...
```  

_memorist_uwsgi.ini_ 파일이 존재하는 위치에서 아래의 명령을 실행한다.  

```
$ uwsgi --ini memorist_uwsgi.ini;
```

```
$ tree .
.
├── manage.py
├── memorist
├── memorist.sock
├── memorist_uwsgi.ini
├── memorist_uwsgi.log
├── staticfiles
...
```  

정상적으로 실행이 되면 `memorist.sock` 소켓 파일이 생성되고, 로그가 `memorist_uwsgi.log`에 기록된다.  

<br/>  

## nginx 설정  

이제 실행시킨 uWSGI와 nginx를 연결해주는 설정이 필요하다.   
_/etc/nginx/sites-available_ 에 위치한 nginx 설정파일을 아래와 같이 변경해준다.    

_memorist.xyz_  

```
upstream django {
	server unix:///home/jihun/Memorist/memorist/memorist.sock;
	# server 127.0.0.1:8001;
}

server {
	listen 80 default_server;
	listen [::]:80 default_server ipv6only=on;

	server_name memorist.xyz www.memorist.xyz;

	location / {
		uwsgi_pass django;
		include /etc/nginx/uwsgi_params;
	}

	location /static {
		alias /home/jihun/Memorist/memorist/staticfiles;
	}
}
```   

- _memorist_uwsgi.ini_ 에서 설정한 _memorist.sock_ 파일의 위치를 지정해준다.    
- uwsgi 를 이용하기 위해 `uwsgi_params` 설정이 필요하다. `uwsgi_params`은 nginx를 설치할 때 기본적으로 포함되있는 파일이다.    
- collectstatic 실행시 생성되는 static 파일들의 디렉토리 위치를 정해준다.   


설정이 완료되었으면 적용하기 위해 아래의 명령을 실행한다.  

```
$ sudo nginx -t
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful

$ sudo service nginx restart
* Restarting nginx nginx                                                [ OK ]
```   

위와 같이 ok 결과가 나오면 된다.  

<br/>  

## 스크립트 파일로 서버 재실행  

서비스의 기능이 추가되는 경우 관련 내용을 적용하여 서버를 다시 시작해야 한다. 기능이 추가될 때마다 반복되는 명령을 실행하는 번거로움을 줄이기 위해 bash 스크립트 파일을 작성하여 사용하고 있다.  

```bash
#!/bin/bash
source ~/.bash_profile
cd /home/jihun/Memorist;
git pull origin master;
pyenv activate Memorist;
pip install -r requirements.txt;
cd /home/jihun/Memorist/memorist;
python manage.py migrate;
python manage.py collectstatic --noinput;
killall uwsgi;
uwsgi --ini memorist_uwsgi.ini;
```

<br/>

## 참고자료   

- <http://uwsgi-docs.readthedocs.io/en/latest/tutorials/Django_and_nginx.html>  
