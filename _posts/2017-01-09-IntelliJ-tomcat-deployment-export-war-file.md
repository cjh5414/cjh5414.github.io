---
layout: post
title:  "IntelliJ Tomcat Deployment - Export war file"
tags:   [IntelliJ]
---

<br/>  

## Tomcat Deployment  

Tomcat을 이용하여 배포할 때 _webapps/_ 폴더 아래에 _myapp.war_ 파일을 위치시키고 tomcat을 실행시키면 _webapps/myapp_ 이름으로 폴더가 생성된다. 이 때 이 프로젝트는 `http://localhost:8080/myapp/index.jsp` 와 같이 url이 지정된다. url에서 myapp을 제거하고 `/`으로 시작하도록 하고 싶다면 _webapps/myapp/_ 하위에 존재하는 파일들을 _webapps/ROOT/_ 하위로 모두 옮기면 `http://localhost:8080/index.jsp` 처럼 사용할 수 있다.  

<br/>  

## Exploded war    

IntelliJ web application project 생성은 [여기](https://cjh5414.github.io/intellij-web-project/)를 참고하면 된다.  
web application을 생성하고 run server을 하면 out이라는 폴더가 생성된다.   

![After run server](/images/intellij-war/after run server.png)  

_out/artifacts/myapp_war_exploded_ 폴더가 존재하는데 war파일이 풀어진 형태이다. 이 폴더를 그대로 톰켓의 _webapps/_ 에 복사해도 배포가 가능하지만 war파일로 만들어서 관리하는 방법도 있다.  

<br/>  

## Export war file  

프로젝트명을 클릭하고 `Command ⌘` + `↓` 키를 눌러서 Project Structure 창을 열고 좌측의 Artifacts를 누른다.  

![open artifacts](/images/intellij-war/open artifacts.png)   

중간에 `myapp:war exploded` 라고 보이는 위 쪽에 + 버튼을 누르면 추가를 할 수 있는데 `Web Application: Archive`의 `For 'myapp:war exploded'`를 선택하면 된다. 기본으로 설정된 myapp의 war exploded에 대한 war파일을 생성하도록 build를 추가하는 것이다.  

![archive](/images/intellij-war/archive.png)  

<br/>  

중간 지점을 보면 `myapp_war.war`으로 이름이 설정되어 있다. 우클릭하여 `myapp.war`으로 이름을 바꿔준다. (위에서도 말했듯이 .war 앞의 이름으로 배포 폴더가 생성되고 url이 정해진다.)

![Rename war file](/images/intellij-war/rename war file.png)  

우측 하단의 Apply 버튼을 클릭한다.  

<br/>  

설정이 완료됐으면 상단의 `Build`의 `Build Artifacts..`를 클릭한다.  

![Build](/images/intellij-war/build.png)  

아래와 같은 창이 뜨면 `myapp:war` -> `Build`를 클릭하여 war을 생성하는 build을 실행할 수 있다.  

![Build artifact](/images/intellij-war/build artifact.png)  

<br/>  

_out/artifacts/myapp_war_ 라는 폴더가 생성되고 _myapp.war_ 파일도 생성 되었다.  

![war file created](/images/intellij-war/war file created.png)   
