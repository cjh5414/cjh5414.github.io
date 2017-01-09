---
layout: post
title:  "IntelliJ, Eclipse Context root[작성중]"
date:   2017-01-09
categories: web
---

<br/>  

## Tomcat Deployment  

Tomcat을 이용하여 배포할 때 `webapps`폴더 아래에 myapp.war 파일을 위치시키고 tomcat을 실행시키면 myapp 이름으로 폴더가 생성된다. 이 때 이 프로젝트는 `http://localhost:8080/myapp/index.jsp` 으로 url이 지정된다. url에서 myapp을 제거하고 `/`으로 시작하도록 하고 싶다면 `webapps/myapp` 하위에 존재하는 파일들을 `webapps/ROOT` 하위로 모두 옮기면 `http://localhost:8080/index.jsp` 처럼 사용할 수 있다.  

<br/>  

## Context root  

Tomcat등을 연결해서 웹프로젝트를 실행할 때 
localhost:8080/[_context root_]/


이 여러개의 어플리케이션을 수행하면
