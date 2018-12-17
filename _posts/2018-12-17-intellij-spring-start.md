---
layout: post
title:  IntelliJ에서 Spring MVC 시작하기 (Maven)
tags:   ['Spring', 'IntelliJ']
---

> IntelliJ에서 Spring MVC 프로젝트를 시작하는 방법에 대해서 알아본다. Spring 프로젝트를 생성하여 초기 환경 설정을 하고 간단한 MVC 코드와 index.jsp 파일을 만들어서 메인 페이지를 띄워본다.  

<br/>  

## 개발 환경
- Java8
- IntelliJ IDEA Ultimate 2018.3
- Spring 4.3.18
- Maven

<br/>  

## 프로젝트 생성  

Spring MVC 를 선택하고 프로젝트명을 입력한후 프로젝트를 생성한다.  

![create project1](/images/intellij-spring-start/create_project1.png)  

![create project2](/images/intellij-spring-start/create_project2.png)  

좌측 project tree의 root directory를 우클릭 하여 Maven Framework를 추가한다.  

![create project3](/images/intellij-spring-start/create_project3.png)
![create project4](/images/intellij-spring-start/create_project4.png)  

<br/>

## Library 설정  

Spring MVC를 선택하여 프로젝트를 생성하였지만 `Spring`, `Spring MVC` library를 사용하기 위해서는 추가적인 작업이 필요하다. `Project Structure` 창을 띄워서 (Mac 에서는 `command` + `;`) Artifacts 탭을 클릭하면 아래와 같은 화면을 볼 수 있다.  

![lib setting1](/images/intellij-spring-start/lib_setting1.png)   

우측에 `Available Elements` 위치한 것들이 이용 가능한 Library 들인데 사용하기 위해서는 double-click 클릭 혹은 drag-and-drop으로 좌측의 `WEB-INF/lib/` 위치로 옮겨야 한다.

![lib setting2](/images/intellij-spring-start/lib_setting2.png)  

좌측의 tree 구조는 빌드 후 생성된 `out` 디렉토리의 구조이다. 이 과정을 수행해야지만 프로젝트를 빌드하면 필요한 library들을 포함 시킬 수 있다.  

> Maven 에 dependencies 를 추가해도 마찬가지의 작업을 수행해줘야 한다.

<br/>  

## Run Configuration 설정   

빌드 후 Web Server를 실행시키기 위해 Run Configuration 설정이 필요하다. 우측 상단의 빌드 관련 drop-down 버튼에 Edit configuration 버튼을 누른다.  

`+` 버튼을 눌러서 Local Tomcat Server 를 선택하고 상단에 적절한 이름을 입력한 후 생성한다.  

![run config1](/images/intellij-spring-start/run_config1.png)  

하단의 경고 메세지를 `Fix` 버튼을 눌러서 수정해 주고 아래의 `Apply` 버튼을 클릭한다.  

![run config2](/images/intellij-spring-start/run_config2.png)  

생성한 Run Configuration 으로 빌드 후 실행해보면 웹 브라우저가 실행되면서 기본으로 생성된 `index.jsp` 파일의 내용을 확인할 수 있다.  

![run config3](/images/intellij-spring-start/run_config3.png)  

<br/>  

## Spring MVC Coding

### controller, view 생성

Home page 처리를 위한 `controller/HomeController.java` 생성  

![index page1](/images/intellij-spring-start/index_page1.png)   

`web/home.jsp` 생성   

![index page2](/images/intellij-spring-start/index_page2.png)   

### 설정 파일 수정     

`web/WEB-INF/dispatcher-servlet.xml` 파일을 아래와 같이 수정한다. ViewResolver의 `prefix`, `suffix`를 설정하고 Annotation 활성화하고 controller 패키지를 스캔할 수 있도록 하는 환경 설정 코드를 추가한다.  

만약 `web/views` directory를 추가하여 jsp 파일들을 관리한다면 prefix를 `/views/`로 설정해주면 된다. 이를 통해 controller가 return 하는 String 값이 어디에 있는 view 인지를 찾도록 해준다.   

![index page3](/images/intellij-spring-start/index_page3.png)   

`web/WEB-INF/web.xml` 파일의 servlet mapping url pattern을 `/`로 설정해준다.

```
<servlet-mapping>
    <servlet-name>dispatcher</servlet-name>
    <url-pattern>/</url-pattern>
</servlet-mapping>
```  

![index page4](/images/intellij-spring-start/index_page4.png)   

<br/>  

### Run  
실행 해보면 작성한 `home.jsp` 가 뜨는 것을 확인할 수 있다.  

![index page5](/images/intellij-spring-start/index_page5.png)  

하지만 URL이 `localhost:8080/[project_name]_war_exploded/`로 되어 있다. context root 를 `/`으로 바꾸기 위해서는 Run/Debug Configurations 로 들어가서 브라우저가 실행될 때 요청하는 기본 URL을 `http://localhost:8080/`으로 바꿔주고  

![index page6](/images/intellij-spring-start/index_page6.png)  

`Deployment` 탭을 클릭해서 Application context 를 `/`로 바꿔준다.

![index page7](/images/intellij-spring-start/index_page7.png)  

다시 실행해보면 context root가 변경된 것을 확인할 수 있다.  

![index page8](/images/intellij-spring-start/index_page8.png)   

<br/>  

## Maven dependency 추가  

Maven dependency를 추가한 후 해당 Library를 이용하기 위해서는 추가적인 설정이 필요하다. Spring Security를 추가하여 사용하는 예시로 살펴보자.  

우선 _pom.xml_ 에 Spring Security dependencies를 추가한다.  

_pom.xml_
```

...

<dependencies>

    ...

    <!--Security-->
    <dependency>
        <groupId>org.springframework.security</groupId>
        <artifactId>spring-security-core</artifactId>
        <version>4.2.3.RELEASE</version>
    </dependency>
    <dependency>
        <groupId>org.springframework.security</groupId>
        <artifactId>spring-security-web</artifactId>
        <version>4.2.3.RELEASE</version>
    </dependency>
    <dependency>
        <groupId>org.springframework.security</groupId>
        <artifactId>spring-security-config</artifactId>
        <version>4.2.3.RELEASE</version>
    </dependency>
</dependencies>
```  

maven import 후에 Project Structures에 들어가 보면 Sprint Security 관련 Libraries이 추가되어 있다. `WEB-INF/lib`로 이동시키면 사용할 수 있다.  

![add lib1](/images/intellij-spring-start/add_lib1.png)   
![add lib2](/images/intellij-spring-start/add_lib2.png)  
