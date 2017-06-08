---
layout: post
title:  Ubuntu timezone 변경하기
tags:   ['Linux']
---

> Ubuntu에서 timezone을 변경하는 방법에 대해서 알아본다. 현재 시간을 알아보는 명령어는 `$ date` 이다.  

<br/>  

timezone을 변경하기 위해서는 아래의 명령을 실행한 뒤 `Aisa` > `Seoul` 을 차례로 선택하면 된다.  

```
$ sudo dpkg-reconfigure tzdata
```  

![tzdata](/images/ubuntu-timezone/tzdata1.png)   

![tzdata](/images/ubuntu-timezone/tzdata2.png)  

```
$ date
2017. 06. 08. (목) 18:37:46 KST
```  
