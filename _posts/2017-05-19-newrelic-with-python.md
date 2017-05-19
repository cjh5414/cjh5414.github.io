---
layout: post
title:  Django(Python) 프로젝트에 New Relic 붙이기
tags:   ['Python']
---

> New Relic이란 모니터링 도구로 어플리케이션 레이어에서부터 서버단의 소프트웨어 레이어까지 다양한 요소들을 웹으로 제공하여 소프트웨어의 장애, 성능 등을 쉽게 파악할 수 있도록 도와준다. 운영중인 Python Django 기반의 서비스에 New Relic을 붙이는 방법에 대해서 알아본다.   

![Result](/images/newrelic/result.png)  

<br/>  

<https://newrelic.com/> 에 접속하여 로그인 후에 `add more`을 클릭하면 언어별로 New Relic 을 사용하는 방법에 대해 나와있다. 설치하는 과정에 대한 오른쪽에 동영상을 오른쪽에 제공하고 있는데 따라해보면 쉽게 적용할 수 있다.  

![Getting start](/images/newrelic/getting start.png)  

<br/>  

pip를 통해 newrelic을 설치한다.  

```
$ pip install newrelic
Collecting newrelic
  Using cached newrelic-2.86.1.66.tar.gz
Installing collected packages: newrelic
  Running setup.py install for newrelic ... done
Successfully installed newrelic-2.86.1.66
```   

`newrelic-admin` 명령으로 _newrelic.ini_ 파일을 생성한다. `<your-key-goes-here>`에는 1번의 license key를 인자로 입력한다.  

```
$ newrelic-admin generate-config <your-key-goes-here> newrelic.ini
$ ls
...
newrelic.ini
```  

_newrelic.ini_ 파일에서 36번째 줄의 `app_name`을 수정한다. 설정한 이름으로 웹에서 확인할 수 있다. 앱이 여러개인 경우 `app_name`으로 구분된다.   

```
$ vim newrelic.ini
...
app_name = LinkU
```    

실행하는 방법은 앱을 구동시키는 명령 앞에 아래와 같이 설정 값을 추가하여 `newrelic-admin run-program`으로 실행해주면 된다.  

```
$ NEW_RELIC_CONFIG_FILE=newrelic.ini newrelic-admin run-program YOUR_ORIGINAL_COMMAND_OPTIONS
```      

[New Relic Python Agent Docs](https://docs.newrelic.com/docs/agents/python-agent?toc=true) 에서 더 자세한 내용을 확인할 수 있다.  
