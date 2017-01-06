---
layout: post
title:  "IntelliJ Web Project 생성 및 실행 (Tomcat Server)"
date:   2017-1-6
categories: java
---

> Eclipse를 이용하여 Java web framework 를 공부 중인데 불편한 점이 있어서 IntelliJ는 어떨까 해서 사용해보려고 한다. 우선 Tomcat을 사용하여 web application 을 생성하고 실행해 보았다.  

<br/>  

## Web Project Create  

IntelliJ를 실행하고 `Create New Project`를 클릭한다.  

![new project](/images/intellij-project/new project.png)  

<br/>  

좌측에서 `Java Enterprise`를 선택하고 Additional Libraries and Frameworks에서 Web Application을 선택한다.

![Web application](/images/intellij-project/web app.png)  

<br/>  

상단을 보면 Application Server를 선택하는 부분이 있는데 좌측에 `New` 버튼을 클릭하여 `Tomcat Server`를 클릭한다.  
tomcat이 위치한 경로를 입력해주면 해당하는 tomcat의 version이 자동으로 뜬다.  

![Tomcat setting](/images/intellij-project/tomcat setting.png)  

project의 이름과 경로를 입력하고 finish를 누르면 프로젝트가 생성된다.  

![project name](/images/intellij-project/project name.png)  

<br/>  

## Project Run  

project를 생성하면 _index.jsp_ 파일이 생성되는데 아래와 같다.  

![index file](/images/intellij-project/index file.png)   

그대로 server를 run 해보면 웹 브라우저가 자동으로 실행되면서 _index.jsp_ 파일이 아래와 같은 결과로 출력된다.     

![Result](/images/intellij-project/result.png)   
