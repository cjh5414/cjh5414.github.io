---
layout: post
title:  MySQL Database 자동으로 백업하기
tags:   ['Linux', 'MYSQL']
---

> MySQL Database를 백업하는 쉘 스크립트와 crontab을 이용해서 특정 시간에 주기적으로 쉘 스크립트를 실행하도록 해서 DB를 자동으로 백업하는 방법에 대해서 알아본다.  

<br/>  

## MySQL Database Dump 명령  

- DB 백업    

```
$ mysqldump -u [user_id] -p[user_password] [DB_name] > [backup_file_name].sql
```  

- DB 복원  

```
$ mysql -u [user_id] -p[user_password] [DB_name] < [backup_file_name].sql
```   

- 기타 테이블 백업 및 복구, 모든 DB 백업 및 복구  

<http://chongmoa.com/sql/630>

<br/>  

## Bash shell script   

_db_backup.sh_  

```bash
#!/bin/bash
DATE=$(date +%Y%m%d)
BACKUP_DIR=/home/user/database_backup/
mysqldump -u abc -pabc123  > $BACKUP_DIR"backup_"$DATE.sql

find $BACKUP_DIR -ctime +3 -exec rm -f {} \;
```   

DATE 변수에 현재 날짜를 저장한다. date 뒤에 띄어쓰기가 있는 것에 주의하자.  
아래와 같이 사용하여 현재 날짜를 알아낼 수 있다.  

```
$ echo $(date +%Y%m%d)
20171118
```   

<br/>  

BACKUP_DIR 변수에는 백업 파일들이 저장될 디렉토리 경로를 저장한다.  

<br/>  

위에서 알아본 `mysqldump` 명령을 이용하여 백업 파일을 생성한다.  

<br/>  

find 명령을 이용하여 3일이 지난 백업파일은 삭제한다.  

`-ctime n` 옵션은 파일의 퍼미션을 마지막으로 변경시킨 날짜를 기준으로 파일을 찾아낸다.   
- +n : n일 또는 n일 이전에 퍼미션이 변경된 파일
- -n : 오늘 부터 n일 전 사이에 퍼미션이 변경된 파일
-  n : 정확히 n일 전에 퍼미션이 변경된 파일

`-exec command {} \;` 옵션은 찾아낸 파일들을 대상으로 특정 명령을 수행한다.    

find 명령어에 대해 아래 링크에 자세히 정리되어있다.  

- <http://geundi.tistory.com/37>  

<br/>  

## crontab 을 이용하여 스크립트 자동 실행  

아래 포스트에 crontab에 대해 자세히 설명되어 있다.  
[Linux crontab 이용하여 작업 명령 예약하기](https://cjh5414.github.io/linux-crontab/)    

```
$ crontab -e
```   

```
...

00 04 * * * /home/user/db_backup.sh
```  
