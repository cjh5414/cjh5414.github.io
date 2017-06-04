---
layout: post
title:  uWSGI를 이용하여 Nginx에 Flask 연결하기
tags:   ['Nginx', 'Python', 'Flask']
---


## Install Flask and uWSGI  

`pip`를 통해 `uwsgi`와 `flask`를 설치한다.  

```
$ pip install uwsgi flask
```  

<br/>  

## Simple Example  

예제로 사용하기 위한 _myproject/_ 라는 폴더를 하나 생성하고 앞으로 이 위치에서 작업한다.  
_test.py_ 파일을 생성하여 Hello를 반환하는 간단한 앱 하나를 생성한다.  

_myproject/test.py_  

```python
from flask import Flask
application = Flask(__name__)

@application.route("/")
def hello():
    return "<h1>Hello!</h1>"

if __name__ == "__main__":
    application.run(host='0.0.0.0')
```   

코드에 이상이 없는지 확인해보기 위해 `test.py`를 실행하고 간단하게 새로운 bash 창을 열어서 `curl` 명령을 이용해서 확인해보자.  

```
$ python test.py
 * Running on http://0.0.0.0:5000/ (Press CTRL+C to quit)
```

```
$ curl http://localhost:5000
<h1>Hello!</h1>
```

<br/>  

## uWSGI  

이제 uWSGI를 이용하여 서버를 구동시키는 방법에 대해 알아보자.  
우선 우리가 작성한 앱과 uwsgi를 연결시켜주기 위해 _wsgi.py_ 파일이 필요하다.  

_myproject/wsgi.py_  

```python
from test import application

if __name__ == "__main__":
    application.run()
```

실행하는 방법은 아래와 같이 하고,

```
$ uwsgi --socket 0.0.0.0:5000 --protocol=http -w wsgi
*** Starting uWSGI 2.0.15 (64bit) on [Sun Jun  4 08:09:54 2017] ***
...
```

> 혹시 앱 이름이 `application`이 아니라 다른 이름으로 했을 경우, `--callable` 옵션을 추가해줘야 된다. ex) $ uwsgi --socket 0.0.0.0:5000 --protocol=http -w wsgi --callable app

결과를 확인해보기 위해 방금 전과 동일한 방식으로 http 요청을 날려본다.  

```
$ curl http://localhost:5000
<h1>Hello!</h1>
```

<br/>  

## Creating a uWSGI Configuration File  

하지만 이런 긴 명령어를 매번 입력하긴 번거롭다. _myproject/myproject.ini_ 파일을 만들어서 관리하면 편리하다.  

_myproject/myproject.ini_  

```
[uwsgi]

module = wsgi

socket = myproject.sock
chmod-socket = 666
vacuum = true

daemonize = /home/user/myproject/uwsgi.log

die-on-term = true
```  

ini 파일에 들어가는 속성들에 대한 설명은 [여기](https://twpower.github.io/linux/2017/04/18/43(ini%ED%8C%8C%EC%9D%BC%EC%9D%84-%ED%86%B5%ED%95%B4-uWSGI-%EC%8B%A4%ED%96%89%ED%95%98%EA%B8%B0).html)에 잘 나와있다.  

<br/>

## socket을 이용한 uWSGI와 Nginx 연결  

> Nginx 를 설치하는 방법은 [여기](https://cjh5414.github.io/ubuntu-deploy-with-django-uwsgi-nginx/)의 초반부를 참고하면된다.  

위에 ini 설정에서 이미 적용했지만 uWSGI와 Nginx를 연결하기 위해 Unix socket을 이용한다. socket을 이용하는 것이 더 안전하고 빠르다. 연결하는 방법은 `.sock` 파일을 통해서 하는데, ini파일에서 설정한 _myproject/myproject.sock_ 이용한다. Nginx에서도 이 파일을 통한 연결을 하도록 설정이 필요하다.

ini 파일에서 지정한 _myproject/myproject.sock_ 파일의 위치를 `uwsgi_pass`에 지정한 것과 같이 입력해주면 된다.  

```
...

server {
        listen  80;
        server_name <ip or domain>;

        ...

        location /myproject {
                include uwsgi_params;
                uwsgi_pass unix:/home/user/myproject/myproject.sock;
        }
}
```  

변경한 내용이 이상이 없는지 확인을 한번 해주고,

```
$ sudo nginx -t
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
```  

이상이 없으면 nginx를 재시작 해주자.  

```
$ sudo service nginx restart
```  

<br/>

## 로그 보는 방법  

nginx 에러 로그는 `var/log/nginx/error.log`에 기록된다.  

```
$ sudo cat /var/log/nginx/error.log
...
```  

uwsgi의 로그를 기록하기 위해서는 먼저 에러 로그를 기록할 log 파일을 생성한다.

```
$ touch uwsgi.log
```  

ini파일에 에러로그를 기록할 파일명과 함께 `daemonize` 설정을 추가해서 백그라운드로 돌리면서 에러를 기록하도록 한다.  

_/myproject/myproject.ini_  

```
...

daemonize = /home/nelp/github-webhooks/uwsgi.log
```   

```
$ sudo cat myproject/uwsgi.log
```  

<br/>  

## 참고자료  

- <https://www.digitalocean.com/community/tutorials/how-to-serve-flask-applications-with-uwsgi-and-nginx-on-ubuntu-14-04#configure-uwsgi>   
