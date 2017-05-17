---
layout: post
title:  Mysql password policy requirements 에러 | validation 제거하여 해결하기
tags:   ['MYSQL']
---

> Mysql에서 유저를 생성하거나 비밀번호를 변경할 때 `mysql ERROR 1819 (HY000): Your password does not satisfy the current policy requirements` 라는 에러가 발생하면 password validation을 제거하는 것으로 간단하게 해결할 수 있다.  

### Error message  

```
mysql> ....
mysql ERROR 1819 (HY000): Your password does not satisfy the current policy requirements
```   

### 해결방법  

```
$ mysql -u root -p
Enter password:
mysql> uninstall plugin validate_password;
Query OK, 0 rows affected (0.08 sec)
```  
