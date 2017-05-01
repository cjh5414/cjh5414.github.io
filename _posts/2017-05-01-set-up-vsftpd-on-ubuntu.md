---
layout: post
title:  Ubuntu Server 에 vsftpd 이용하여 파일 전송하기
tags:   ['Linux']
---

### vsftpd 설치  

```
$ sudo apt-get update
$ sudo apt-get install vsftpd
```  

설치가 완료되면 기본 설정파일을 백업해두자.

```
$ sudo cp /etc/vsftpd.conf /etc/vsftpd.conf.orig
```  

<br/>  

### ufw를 이용한 방화벽 설정  

```
$ sudo apt-get install ufw
```  

ufw를 설치하고, FTP port를 허용해준다.

```
$ sudo ufw allow 20/tcp
$ sudo ufw allow 21/tcp
$ sudo ufw enable
```  

```
$ sudo ufw status verbose

Status: active

To                         Action      From
--                         ------      ----
20/tcp                     ALLOW       Anywhere
21/tcp                     ALLOW       Anywhere
20/tcp (v6)                ALLOW       Anywhere (v6)
21/tcp (v6)                ALLOW       Anywhere (v6)
```  

으로 결과를 확인해볼 수 있다.  

<br/>  

### 특정 User의 local shell에서 FTP 사용할 수 있도록 설정하기  

특정 사용자가 shell을 통해 FTP connection을 하고 파일들을 읽고 쓰게 할 수 있도록 _/etc/vsftpd.conf_ 파일을 통해서 설정해준다.  

```
$ sudo vim /etc/vsftpd.conf
```  

파일을 열어서 아래와 같이 해당하는 부분들의 값들을 설정해주면 된다.  

_/etc/vsftpd.conf_  
```
...

# Allow anonymous FTP? (Disabled by default).
anonymous_enable=NO
#
# Uncomment this to allow local users to log in.
local_enable=YES

...

write_enable=YES

...

chroot_local_user=YES
```  

지정된 사용자만 접속할 수 있도록 설정한다.  

```
userlist_enable=YES
userlist_file=/etc/vsftpd.userlist
userlist_deny=NO
```   

`userlist_deny`를 `YES`로 하면 userlist에 있는 사용자들은 FTP 접근이 불가능 하고, 반대로 `NO`로 설정하면 userlist에 있는 사용자들만 FTP 접근이 가능하다.  
FTP를 허용해줄 사용자를 _/etc/vsftpd.userlist_ 에 추가한다.  

```
$ echo "user_name" | sudo tee -a /etc/vsftpd.userlist
$ cat /etc/vsftpd.userlist
user_name
```  

설정이 완료됐으면 vsftpd를 재시작 한다.  

```
$ sudo systemctl restart vsftpd
```   

<br/>  

### 결과 확인  

CLI에서 ftp명령으로 서버의 아이피를 입력하고 실행하고 user와 password를 입력한다.   

```
$ ftp -p 123.123.123.123

Connected to 123.123.123.123.
220 (vsFTPd 3.0.3)
Name (203.0.113.0:default): user_name
331 Please specify the password.
Password: user's_password
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp>
```  

`get`, `put` 으로 원하는 파일을 가져오거나 올릴 수 있다.  

```
ftp> cd files
ftp> get test.txt
ftp> put test.txt upload.txt
ftp> bye
```  

<br/>  

### 참고자료  

- <https://www.digitalocean.com/community/tutorials/how-to-set-up-vsftpd-for-a-user-s-directory-on-ubuntu-16-04#step-4>
