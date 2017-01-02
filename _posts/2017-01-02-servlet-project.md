---
layout: post
title:  "간단한 login 기능의 Servlet Project"
date:   2016-1-2
categories: java
---

<br/>  

> Eclipse를 이용하여 간단한 login 기능의 Servlet Project를 만들어 본다.  

### Project Create  

File -> New -> Dynamic Web Project 를 선택하여 새로운 프로젝트를 생성한다.   

![project create](/images/servlet/project-create.png)   

<br/>  

프로젝트 이름을 입력하고 web.xml 파일을 생성한다는 체크박스를 선택하고 Finish 버튼을 누른다.  

![web module](/images/servlet/web module.png)   

<br/>  

web.xml 파일을 열어보면 `welcome-file`라는 태그가 많이 존재하는 것을 볼 수 있다. 일반적으로 웹 사이트를 url을 통해 호출하면 `www.google.com/index.html` 같이 뒷 부분에 반환되는 file을 사용자가 명시하지 않더라도 welcome-file 리스트 중에 선택하여 반환 해준다.  

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://xmlns.jcp.org/xml/ns/javaee" xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_3_1.xsd" id="WebApp_ID" version="3.1">
  <display-name>helloServlet</display-name>
  <welcome-file-list>
    <welcome-file>index.html</welcome-file>
    <welcome-file>index.htm</welcome-file>
    <welcome-file>index.jsp</welcome-file>
    <welcome-file>default.html</welcome-file>
    <welcome-file>default.htm</welcome-file>
    <welcome-file>default.jsp</welcome-file>
  </welcome-file-list>
</web-app
```  

<br/>  

### Create index.html   

haha
