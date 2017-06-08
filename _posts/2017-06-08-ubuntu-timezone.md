---
layout: post
title:  Ubuntu timezone 변경하기
tags:   ['Linux']
---

> Ubuntu에서 timezone을 변경하는 방법에 대해서 알아본다. 현재 시간을 알아보는 명령어는 `$ date` 이다.  

```
$ sudo dpkg-reconfigure tzdata
```  

![tzdata](/images/ubuntu-timezone/tzdata1.png)   

![tzdata](/images/ubuntu-timezone/tzdata2.png)  

```
$ date
2017. 06. 08. (목) 18:37:46 KST
```  
