---
layout: post
title:  Linux crontab 이용하여 작업 명령 예약하기
tags:   ['Linux']
---

> Linux의 crontab은 스케줄링을 관리하는 프로그램으로 `매일 0시 0분에 DB를 백업한다.`, `1시간 마다 00작업을 수행한다.` 등의 특정 시간에 특정 작업을 수행 하도록 명령을 예약할 수 있다.   

<br/>  

## crontab 명령어  

### 예약된 작업 리스트  

현재 예약된 작업의 리스트를 보여준다.  

```
$ crontab -l
```  

<br/>  

### 예약된 작업 수정

새로운 작업을 추가하기 위해서는 `-e` 옵션을 이용한다. 특정 파일을 통해 관리 하는데 default로 주석에 사용하는 방법이 나와있다. 맨 아래에 원하는 명령을 입력하고 저장하면 된다. 다시 명령을 실행하면 이전의 내용을 수정, 추가할 수 있다.  

```
$ crontab -e
```  

```
# Edit this file to introduce tasks to be run by cron.
#
# Each task to run has to be defined through a single line
# indicating with different fields when the task will be run
# and what command to run for the task
#
# To define the time you can provide concrete values for
# minute (m), hour (h), day of month (dom), month (mon),
# and day of week (dow) or use '*' in these fields (for 'any').#
# Notice that tasks will be started based on the cron's system
# daemon's notion of time and timezones.
#
# Output of the crontab jobs (including errors) is sent through
# email to the user the crontab file belongs to (unless redirected).
#
# For example, you can run a backup of all your user accounts
# at 5 a.m every week with:
# 0 5 * * 1 tar -zcf /var/backups/home.tgz /home/
#
# For more information see the manual pages of crontab(5) and cron(8)
#
# m h  dom mon dow   command
```

<br/>  

### 작업 리스트 삭제  

`-e` 옵션으로 추가한 작업 명령이 삭제된다.  

```
$ crontab -r
```  

<br/>  

## 작업 명령 이용 방법  

아래와 같은 형식으로 예약 명령어를 이용한다. 예시로 이해하는 것이 훨씬 쉽다.  

```
* * * * * 수행할 명령어
┬ ┬ ┬ ┬ ┬
│ │ │ │ └───────── 요일 (0 - 6) (0:일요일, 1:월요일, 2:화요일, …, 6:토요일)
│ │ │ └───────── 월 (1 - 12)
│ │ └───────── 일 (1 - 31)
│ └───────── 시 (0 - 23)
└───────── 분 (0 - 59)
```   

<br/>  

## 예시  

> ### ```* * * * * /home/user/test.sh```  

- 1분 마다 `/home/user/test.sh` 를 수행.  

<br/>  

> ### ```30 10,22 * * * /home/user/test.sh```  

- 매일 오전 10시 30분, 오후 10시 30분에 `/home/user/test.sh` 를 수행.  

<br/>  

> ### ```*/10 * * * * /home/user/test.sh```  

- 10분 마다 `/home/user/test.sh` 를 수행.   

<br/>  

> ### ```0 8 * * 1-5 /home/user/test.sh```  

- 평일(월~금요일) 8:00 시에 `/home/user/test.sh` 를 수행.  


<br/>  

## timezone 변경  

사용 중인 OS의 timezone이 맞지 않으면 crontab이 원하는 시간에 실행되지 않을 수 있다. `$ date` 명령으로 확인해보고 맞지 않으면 timezone을 변경해줘야 한다.  

```
$ date
Wed Jun  7 23:41:57 KST 2017
```  

timezone을 변경하는 방법은 아래의 링크를 참고하면 된다.  

- [Timezone 변경하기](https://cjh5414.github.io/)

timeznoe 을 변경한 후에는 crontab을 재시작 해야 적용된다.  

```
$ service cron restart
```  

<br/>  

## 참고자료  

- <https://zetawiki.com/wiki/%EB%A6%AC%EB%88%85%EC%8A%A4_%EB%B0%98%EB%B3%B5_%EC%98%88%EC%95%BD%EC%9E%91%EC%97%85_cron,_crond,_crontab>
