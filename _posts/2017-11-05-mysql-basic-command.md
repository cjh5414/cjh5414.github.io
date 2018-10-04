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

- 특정 테이블 정보 조회  

```
mysql> desc [table_name];
```  

- 컬럼 추가

```
mysql> alter table [table_name] add [column_name] varchar(100) not null default '0';
```  

- 테이블 삭제  

```
mysql> drop table [table_name];
```     

- 테이블 삭제 (외래키 검사 설정)  

```
mysql> SET foreign_key_checks = 0;
Query OK, 0 rows affected (0.00 sec)
mysql> drop table [table_name];
Query OK, 0 rows affected (0.00 sec)
mysql> SET foreign_key_checks = 1;
Query OK, 0 rows affected (0.00 sec)
```   

<br/>  

### Select   

- 그룹핑

```
mysql> SELECT [column_names] FROM [table_name] GROUP BY [column_name];
```
