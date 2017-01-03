---
layout: post
title:  "tomcat을 이용한 servlet 배포"
date:   2017-1-3
categories: java
---

<br/>

## Eclipse war 파일 생성  

eclipse에서 해당 프로젝트를 우클릭하여 Export 기능을 선택한다.  
Web -> WAR file을 선택한다.  

![Export](/images/servlet-deploy/export.png)   

위 그림과 같이 Destination을 Tomcat이 위치한 폴더 아래의 `webapps` 아래에 war 파일이 위치하도록 설정해줘야 한다.  


해당 위치로 가보면 war 파일이 생성된 것을 확인할 수 있다.  

![war file](/images/servlet-deploy/warfile.png)  

<br/>  

## Tomcat Run

> Tomcat을 실행할 때는 항상 Eclipse의 tomcat이 실행되고 있는지 확인하고 종료한 후에 실행하는 것이 좋다.  

<br/>  

Tomcat을 실행시키면 webapps에 존재하는 war 파일을 압축을 풀어서 폴더를 생성하고 실행한다.  

![Tomcat run](/images/servlet-deploy/tomcat run.png)  
