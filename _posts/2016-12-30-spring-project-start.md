---
layout: post
title:  "Spring project start"
date:   2016-12-30
categories: spring
---

> 이클립스를 통해서 기본적인 spring 프로젝트를 생성하는 방법에 대해서 알아본다.  

<br>  

### Project Create  

좌측 상단의 프로젝트 생성 버튼을 누르고 `Ohter...` 를 클릭한다. 혹은 `command + N`를 누른다.  

![project create](/images/spring-project/project create.png)  

<br>  

Spring -> Spring Legacy Project 를 선택한다.  

![spring legacy](/images/spring-project/spring legacy.png)  

<br>  

Project name을 입력하고 MVC Project를 선택한다.  

![MVC Project](/images/spring-project/MVC Project.png)   

패키지 명을 입력하고 Finish를 누르면 프로젝트가 생성된다.  

<br>  

### Project Run   

Deployed Resources > WEB-INF > views > home.jsp 파일을 보면 기본적으로 작성된 jsp 코드가 있다. 'Home' 라는 title과 'Hello world!' 문구와 현재 시간을 보여주는 코드이다.  

![home jsp file](/images/spring-project/home jsp.png)   

<br>  

아래와 같은 방법으로 tomcat 서버에서 실행해 볼 수 있다.

![run on server](/images/spring-project/run on server.png)   

<br>  

tomcat v8.0을 선택하고 finish를 클릭한다.  

![set tomcat](/images/spring-project/set tomcat.png)   

<br>  

새로운 창에 결과가 뜨는 것을 확인할 수 있다.  

![run result](/images/spring-project/run result.png)  

<br>  

웹 브라우저를 통해 접속을 해보면 같은 결과를 확인 할 수 있다.    

![run on browser](/images/spring-project/run on browser.png)  
