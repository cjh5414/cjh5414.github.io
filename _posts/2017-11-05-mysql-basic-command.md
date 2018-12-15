---
layout: post
title:  Mysql 명령어 정리
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

- 중복된 레코드 제거   

```
mysql> SELECT DISTINCT [column_names] FROM [table_name];
```  

- 그룹핑

```
mysql> SELECT [column_names] FROM [table_name] GROUP BY [column_name];
mysql> SELECT [column_names] SUM(column_name) FROM [table_name] GROUP BY [column_name];
```  

```
mysql> SELECT year, semester, sum(credit) credit FROM courses GROUP BY year, semester;
```

- 레코드 순서 지정 (Default : ASC, 내림차순 : DESC)

```
mysql> SELECT [column_names] FROM [table_name] ORDER BY [column_name];
mysql> SELECT [column_names] FROM [table_name] ORDER BY [column_name] DESC;
```    

- LIKE 연산자   
    - _ : 임의의 한 개 문자를 의미한다.   
    - % : 임의의 여러 개 문자를 의미한다.  

```
mysql> SELECT [column_names] FROM [table_name] WHERE [column_name] LIKE '김%';
```    

#### 집합 연산  
두 SELECT 문의 필드의 개수와 데이터 타입이 서로 같아야 한다.  

- 합집합 UNION (Default: distinct, 중복 포함하고 싶다면 UNION ALL)

```
mysql> SELECT [column_names] FROM [table_name1]
mysql> UNION ALL
mysql> SELECT [column_names] FROM [table_name2];

ex)
mysql> SELECT id from student UNION ALL SELECT id from professor;
```  

- 교집합 INTERSECT  

```
mysql> SELECT [column_names] FROM [table_name1]
mysql> INTERSECT
mysql> SELECT [column_names] FROM [table_name2];

ex) 컴퓨터공학부 학생들 중에서 A+ 를 받은 교과목이 있는 학생  
mysql> SELECT id from student
mysql> FROM student s, department d
mysql> WHERE s.id = d.id and dept_name='컴퓨터공학부'
mysql> INTERSECT
mysql> SELECT id
mysql> takes
mysql> grade = 'A+';
```  

- 차집합 MINUS  

```
mysql> SELECT [column_names] FROM [table_name1]
mysql> MINUS
mysql> SELECT [column_names] FROM [table_name2];
```  

<br/>

### Delete

```
mysql> DELETE FROM [table_name] WHERE [condition];

ex)
mysql> DELETE FROM offers WHERE id > 7;
```  

