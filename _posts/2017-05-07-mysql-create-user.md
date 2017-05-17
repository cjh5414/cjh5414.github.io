---
layout: post
title:  Mysql 사용자 추가, 제거 및 권한 부여
tags:   ['Mysql']
---

### Mysql 서버 로그인  

먼저 사용자를 추가하기 위해 로그인이 필요하다.  

```
$ mysql -u root -p
Enter password:
```  

<br/>  

### 로컬에서 접속 가능한 사용자 추가하기  

```
$ create user '사용자'@'localhost' identified by '비밀번호';
```

> `mysql ERROR 1819 (HY000): Your password does not satisfy the current policy requirements` 라는 에러가 발생하면 [Mysql password policy requirements 에러 | validation 제거하여 해결하기](https://cjh5414.github.io/mysql-create-user/) 을 참고하자.  

<br/>  

### DB 권한 부여하기  

```
$ grant all privileges on *.* to '사용자'@'localhost';
$ grant all privileges on DB이름.* to '사용자'@'localhost';
```

모든 DB에 접근 가능하도록 하려면 `*.*`, 특정 DB에만 접근 가능하도록 하려면 `DB이름`으로 지정해주면 된다.  

<br/>  

### 사용자 계정 삭제  

```
$ drop user '사용자'@'localhost';
```  

<br/>  

### 원격에서 접속 가능한 사용자

원격에서 접속 가능한 사용자를 생성하려면 위애서 `localhost`만 `IP`로 바꿔서 입력해주면 된다.  
