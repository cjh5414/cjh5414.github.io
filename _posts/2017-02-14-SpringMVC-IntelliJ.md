---
layout: post
title:  Start SpringMVC project in IntelliJ
tags:   [Spring, IntelliJ]
---

> IntelliJ에서 SpringMVC 프로젝트로 "Hello world"를 띄우는데 막히는 부분이 있어서
 정리해놓고 나중에 참고하고자 한다. 단순히 SpringMVC Project를 생성한다고 해서 동작하지 않고,
  프로젝트를 생성할 때 자동으로 추가되는 라이브러리들이 서버를 실행시킬 때 찾을 수 있도록 지정해주는 작업이 필요하다.   

<br/>  

## SpringMVC Project 생성  

SpringMVC 프로젝트를 선택한 후 `Next` 버튼을 누른다.  

![new project](/images/intellij-springMVC/new project.png)  

프로젝트명을 입력하고 `Finish` 버튼을 누른다.  

![project name](/images/intellij-springMVC/project name.png)  

<br/>  

## Maven Project로 설정  

Maven Project로 설정하기 위해 프로젝트명을 우클릭하여 `Add Frameworks Support` 를 클릭한 후 Maven을 선택한다.  

![add maven](/images/intellij-springMVC/add maven.png)  

<br/>  

## Tomcat server를 이용하여 실행  

프로젝트를 실행시켜보기 위해 `Add New configuration` 을 클릭하여 Run 환경 설정을 한다.  

![edit configuration](/images/intellij-springMVC/edit configuration.png)  

상단의 `+` 버튼을 클릭하여 `Tomcat Server` 의 `local` 을 선택한다.   

![run config fix](/images/intellij-springMVC/run config fix.png)   

하단에 배포에 artifact가 없다는 Warning이 뜨므로 `fix` 을 눌러준다.  

그대로 실행해보면 아래와 같은 메세지가 뜬다.  

```
Connected to server
[2017-02-15 12:06:48,662] Artifact hellpSpringMVC:war exploded: Artifact is being deployed, please wait...
15-Feb-2017 00:06:49.827 심각 [RMI TCP Connection(2)-127.0.0.1] org.apache.catalina.core.StandardContext.startInternal One or more listeners failed to start. Full details will be found in the appropriate container log file
15-Feb-2017 00:06:49.848 심각 [RMI TCP Connection(2)-127.0.0.1] org.apache.catalina.core.StandardContext.startInternal Context [] startup failed due to previous errors
[2017-02-15 12:06:49,902] Artifact hellpSpringMVC:war exploded: Error during artifact deployment. See server log for details.
15-Feb-2017 00:06:58.511 정보 [localhost-startStop-1] org.apache.catalina.startup.HostConfig.deployDirectory Deploying web application directory /usr/local/apache-tomcat-8.0.39/webapps/manager
15-Feb-2017 00:06:58.703 정보 [localhost-startStop-1] org.apache.catalina.startup.HostConfig.deployDirectory Deployment of web application directory /usr/local/apache-tomcat-8.0.39/webapps/manager has finished in 191 ms
```  

<br/>  

## Artifats 설정  

SpringMVC 프로젝트를 생성하면 관련 라이브러리를 다운받아서 사용하는데 `External Libraries` 에 추가되는 것이 아니라 프로젝트 내부에 `lib`라는 폴더 아래에 포함돼 있다. 아직 정확한 이유는 잘 모르겠지만 artifact에 몇몇 라이브러리가 추가돼있지 않아서 오류가 발생한 듯하다.  

아래와 같이 추가하여 해결하면 제대로 동작한다.  

`Project Structure` 에 들어가서 Artifacts를 클릭한다.  

![fix artifact](/images/intellij-springMVC/fix artifact.png)  

`Fix` 버튼을 클릭한 후 아래와 같이 모든 dependencies를 artifact에 추가하는 작업을 해준다.  

![all artifact](/images/intellij-springMVC/all artifact.png)  

다시 실행하여 결과를 보면 제대로 동작한다.  

![end result](/images/intellij-springMVC/end result.png)  
