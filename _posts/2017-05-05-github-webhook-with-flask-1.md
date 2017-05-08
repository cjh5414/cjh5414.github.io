---
layout: post
title:  Flask와 Github Webhook 을 이용하여 pull request 생성 될 때마다 자동으로 알림 받기1
tags:   ['Git', 'Python', 'Flask']
---

> Github의 Webhooks 기능을 이용하면 저장소에 push, comment, issues 등의 이벤트가 발생했을 때마다 알림을 받을 수 있다. Github Webhooks에 URL을 등록하면 특정 이벤트가 발생할 때 해당하는 정보들을 POST 요청으로 날려준다. 이번 포스트에서는 서버에서 Flask으로 요청을 처리하기 위한 설정과 Github 에서 Webhook URL 등을 지정하는 등의 기본적인 설정에 대해서 다룬다.  

<br/>  

### 사전준비  

- 이밴트가 발생하는 Git Repository
- Webhook 요청을 처리해줄 서버
- 서버에서 Nginx를 이용

<br/>  

### Server에 Flask 설치 및 설정  

Event가 발생할 때 Webhook에서 보내는 요청들을 처리하기 위해 `Flask`를 이용한다.

`Flask`를 이용하기 위해서는 `python`과 `pip`가 필요하다.  
(python version3을 이용한다.)  

```
$ sudo apt-get update
$ sudo apt-get dist-upgrade
$ sudo apt-get autoremove
$ sudo apt-get install python3
$ sudo apt-get install python3-pip
$ sudo pip3 install --upgrade pip
```  

<br/>  

`pip3`을 이용하여 `Flask`를 설치한다.  

```
$ sudo pip3 install Flask
```  
<br/>  

`Flask` 프로젝트를 담을 폴더와 파일을 생성한다.  

```
$ mkdir github-webhooks
$ cd github-webhooks
$ vim webhooks.py
```  

webhooks에 지정할 URL은 `[server_url]/postreceive`을 사용하고 테스트를 위해 `Hello World`를 반환하는 API를 작성한다. (`POST` 방식을 이용한다.)  

_webhooks.py_  

```python
from flask import Flask
app = Flask(__name__)

@app.route('/postreceive', methods=['POST'])
def hello_world():
    return 'Hello World!'

if __name__ == '__main__':
    app.run()
```    

```
python3 webhooks.py
 * Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)
```  

작성한 코드를 실행시켜보면 5000포트로 localhost에서 동작한다.  

<br/>  

### Nginx, Flask 연결  

`[server_url]/postreceive` URL로 들어오는 요청을 Flask로 연결해주기 위한 Nginx 설정이다.

_/etc/nginx/nginx.conf_    

```
http {
    ...
    server {
        listen  80;
        server_name [server_url];

        ...

        location /postreceive{
                proxy_pass http://localhost:5000;
        }
    }
}
```  

Nginx 설정이 변경되면 설정파일을 반영하기 위해 아래의 명령을 실행시켜줘야 한다.  

```
$ sudo service nginx reload;
```  

이제 `[server_url]/postreceive` URL로 POST 요청을 하면 Flask를 통해 `Hello World!`를 반환한다.  

<br/>  

### Github Webhooks 설정  

> Git Repository > settings > Webhooks > Add webhook  

![add webhook](/images/github-webhook/add webhook.png)

- Payload URL  : `[server_url]/postreceive` 입력  
- Content type : Application/json 선택  
- Secret       : 일단 비워 둠  

위의 내용을 입력한 후에 `Add webhook` 버튼을 눌러서 Webhook을 생성하면 자동으로 테스트 Request을 보낸다.  
결과는 `Recent Deliveries` 에서 확인할 수 있다. 정상적으로 동작 했다면 Response에 200 status code로 `Hello World!`라는 메세지가 출력될 것이다.   

![test deliver](/images/github-webhook/test deliver.png)  

다음 포스트에서는 `pull_request` 이밴트를 등록하여 Pull Request이 생성될 때마다 알림을 받는 방법에 대해서 알아본다.  
