---
layout: post
title:  Mysql 기본적인 명령어 정리
tags:   ['MYSQL']
---

### Mac 에서 Mysql 서버 실행   

```
$ mysql.server start
```  

<br/>  

### Root 로그인

```
$ mysql -u root -p
```  

<br/>  

### Database  

- 생성    

```
mysql> create database DB_NAME;
```  

- 조회  

```
mysql> show databases;
```   

- 삭제  

```
mysql> drop database [database_name];
```   

- 사용

```
mysql> use [database_name];
```  

- 현재 사용중인 DB 확인   

```
mysql> select database();
```

<br/>

### Table  

- 조회  

```
mysql> show tables;
```
