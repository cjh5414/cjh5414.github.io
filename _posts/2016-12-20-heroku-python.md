---
layout: post
title:  "python 코드를 Heroku에 올리고 특정 시간에 실행하도록 하기"
date:   2016-12-20
categories: python
---

> python으로 학식 알림 봇을 만들었는데 서버가 필요했다. Heroku를 사용했고 특정 시간에 알림 프로세스를 규칙적으로 실행 시켜주는 방법을 적용해봤다.  

<br>  

## Heroku 설치  

[Heroku](https://dashboard.heroku.com/apps)에 가입을 하고 [Heroku Dev Center](https://devcenter.heroku.com/articles/heroku-cli#download-and-install) 에서 운영체제에 맞는 installer을 다운받아서 설치한다.  

<br>  

## Heroku에 python code 올리기  

먼저 git으로 관리되고 있는 프로젝트 폴더로 이동한다.   
(배포에 git을 이용하기 때문에 설치가 되어있어야 한다)   

CLI를 이용하여 login을 하고 heroku에 앱을 생성한다.

```
$ heroku login
Enter your Heroku credentials.
Email:
Password (typing will be hidden):
Authentication successful.

$ heroku create
Creating app... done, ⬢ [heroku_app_name]
https://[heroku_app_name].herokuapp.com/ | https://git.heroku.com/[heroku_app_name].git
```   

앱이 생성되었다는 메세지와 함께 생성된 앱의 이름(heroku_app_name)이 표시된다.  
<https://dashboard.heroku.com/apps>에 가서 로그인하여 확인해보면 동일한 이름의 앱이 생성된 것을 확인할 수 있다. 또한 앱이 생성됨과 동시에 `heroku` 라는 이름의 git remote가 자동으로 설정된다.  

```
$ git remote -v
heroku	https://git.heroku.com/[heroku_app_name].git (fetch)
heroku	https://git.heroku.com/[heroku_app_name].git (push)
origin	https://github.com/[...].git (fetch)
origin	https://github.com/[...].git (push)
```  

위의 명령으로 확인해 볼 수 있다.  

이제부터는  

```
$ git push heroku master
```  

라는 명령으로 push를 하면 heroku에 배포를 할 수 있다.  

<br>   

## 필요한 파일  

python을 heroku에 배포하기 위해서는 몇가지 파일들을 더 추가해줘야 한다.  

- __requirements.txt__  

```
$ pip freeze > requirements.txt
```  
명령으로 현재 프로젝트에 사용되고 있는 패키지들의 리스트를 저장해서 heroku에서 배포할 때도 설치할 수 있도록 해줘야한다.  

- __runtime.txt__   

```
$ echo 'python-3.5.2' > runtime.txt
```
사용하고 있는 python version을 명시해줘야한다.  

- __Procfile__  

```
$ echo 'clock: python 파일명.py' > Procfile
```  

서버에서 실행할 명령어를 넣어준다.  
명령어 앞에 process type을 지정하는데  

- web  
- worker  
- urgentworker  
- clock  

등을 사용할 수 있지만 정확한 사용법은 잘 모르겠다.  
clock을 사용한 이유는 Scheduler를 이용하여 특정 시간에 주기적으로 process를 실행시키기 위함이다.  

파일 작성이 완료됐으면   

```
git add [...]
git commit [...]
git push heroku master
```  

으로 원격 저장소에 push 해주면 된다.  

<br>  

## 빌드팩 설치  

python에 해당하는 빌드팩을 설치해줘야 한다.  

```
$ heroku buildpacks:set heroku/python
$ heroku create --buildpack heroku/python
```  

자세한 내용은
[heroku buildpacks](https://devcenter.heroku.com/articles/buildpacks)를 참고하면 된다.  

<br>  

## APScheduler 이용하기  

python에 지정한 시간마다 특정 함수를 실행시킬 수 있도록 해주는 패키지가 존재한다.  

```
$ pip install apscheduler
```  

새로운 패키지를 설치했으니 requirements.txt을 업데이트 해야한다.  

```
$ pip freeze > requirements.txt
```  

```python
from apscheduler.schedulers.blocking import BlockingScheduler

sched = BlockingScheduler()

@sched.scheduled_job('interval', minutes=3)
def timed_job():
    print('This job is run every three minutes.')

@sched.scheduled_job('cron', day_of_week='mon-fri', hour=17)
def scheduled_job():
    print('This job is run every weekday at 5pm.')

sched.start()
```  

첫 번째 지정한 데코레이터(`@sched.scheduled_job('interval', minutes=3)`)는 3분 간격으로 아래의 함수를 호출한다는 의미로 `This job is run every three minutes.` 이라는 메세지를 출력한다.  

두 번째 지정한 데코레이터(`@sched.scheduled_job('cron', day_of_week='mon-fri', hour=17)`)는 평일 17시 마다 `scheduled_job()` 함수를 호출하여 `This job is run every weekday at 5pm.`이라는 메세지를 출력한다.  

`sched.start()`를 통해 scheduler가 동작을 시작한다.  

<br>  

## timezone 맞추기  

> 원하는 시간에 프로그램이 실행되도록 하려면 timezone이 나의 시간과 일치해야한다.  

```
SEOUL_TZ = pytz.timezone('Asia/Seoul')
today_index = datetime.now(SEOUL_TZ).weekday()
```  

위의 코드는 timezone을 `Asia/Seoul`로 설정하고 오늘이 무슨 요일인지를 index로 알려주는 예시이다.(월요일 = 0)     

```
$ heroku config:add TZ="Asia/Seoul"
```  

heroku 서버의 timezone도 맞춰서 설정해줘야 한다. CLI를 통해서 timezone을 지정하여 위의 명령을 실행하면 된다.  

<br>  

## 참고자료  

<https://devcenter.heroku.com/categories/python>  
<https://devcenter.heroku.com/articles/clock-processes-python>  
<https://hyesun03.github.io/2016/10/10/heroku/>  
