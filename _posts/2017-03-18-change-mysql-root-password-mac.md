---
layout: post
title:  MySQL root password 변경
tags:   [MySQL]
---

> mysql의 root 비밀번호를 변경할 때 `update user set password=password('1234') where user='root';` 명령을 입력했는데 `ERROR 1054 (42S22): Unknown column 'password' in 'field list'` 오류가 발생했다면 user table에 `password` 필드가 존재하지 않기 때문이다. `password` 대신 `authentication_string` 필드가 존재할 것이므로 `update user set authentication_string=password('1234') where user='root';` 을 이용하면 된다.  

<br/>  

### mysql 접속

```
$ mysql -u root -p
Enter password:
```   

### mysql database 선택  

```
mysql> use mysql;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
```  

### 비밀번호 변경  

검색을 해보면 `update user set password=password('1234') where user='root';` 명령을 이용하면 된다고 나와있다.  
그대로 복사해서 입력해보면  

```
mysql>  update user set password=password('1111') where user='root';
ERROR 1054 (42S22): Unknown column 'password' in 'field list'
```  

`password` field 를 찾을 수 없다는 에러 메세지가 출력된다.  

확인해보기 위해 user table의 field 들을 출력해봤더니  

```
mysql> describe user;
+------------------------+------------------------+------+-----+---------+-------+
| Field                  | Type                   | Null | Key | Default | Extra |
+------------------------+------------------------+------+-----+---------+-------+
| Host                   | char(60)               | NO   | PRI |         |       |
| User                   | char(32)               | NO   | PRI |         |       |
| ...                                                                            |
| authentication_string  | text                   | YES  |     | NULL    |       |
| password_expired       | enum('N','Y')          | NO   |     | N       |       |
| password_last_changed  | timestamp              | YES  |     | NULL    |       |
| password_lifetime      | smallint(5) unsigned   | YES  |     | NULL    |       |
| account_locked         | enum('N','Y')          | NO   |     | N       |       |
+------------------------+------------------------+------+-----+---------+-------+
45 rows in set (0.00 sec)
```  

`password` field를 찾을 수 없었다. 대신 `authentication_string` 이라는 field가 존재한다.  
위의 명령에서 `password` 대신 `authentication_string`을 업데이트하는 것으로 수정하여 실행하면 제대로 동작한다.  

```
mysql> update user set authentication_string=password('1234') where user='root';
Query OK, 1 row affected, 1 warning (0.00 sec)
Rows matched: 1  Changed: 1  Warnings: 1
```  

변경사항을 적용하기 위해 아래 명령을 실행한 후에 종료하면 비밀번호 변경이 완료된다.  

```
mysql> flush privileges;
mysql> quit
Bye
```    
