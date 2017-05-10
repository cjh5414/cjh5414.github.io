---
layout: post
title:  IntelliJ에서 Springboot 프로젝트 war file로 배포하기
tags:   [Spring, IntelliJ]
---

> Springboot 프로젝트를 war파일로 추출해서 외부 tomcat으로 돌리는 방법에 대해서 알아본다.  

### Gradle

_build.gradle_  

```
apply plugin: 'war'
war {
	baseName = '[war 파일 명]'
}

...

dependencies {
	...
	providedRuntime('org.springframework.boot:spring-boot-starter-tomcat')
}
```  

<br/>  

### Gradle Builde & War file 생성  

![gradle](/images/war-file/gradle build.png)  

우측 바의 Gradle 메뉴를 클릭하면 Gradle Projects 창이 나타난다. 첫번 째 버튼을 클릭하여 Gradle 동기화를 해준 후에 `Run Configurations` 의 `프로젝트[war]`을 실행시켜주면 War 파일이 생성된다.  

<br/>  

### 생성된 War file 위치  

> 프로젝트 > build > libs

<br/>  

### War file 생성 후 Application 구동  

springboot는 tomcat이 없어도 가상 어플리케이션 서버를 이용하여 구동시킬 수 있다.
위에 설정을 적용하는 gradle을 돌린 후에 Application을 실행하면 외부 tomcat을 이용하는 것으로 설정이 바뀌기 때문에 컴퓨터에 tomcat이 설치돼 있지 않으면 에러가 발생한다.

war 파일을 생성하는 것이 아니라 로컬에서 서버를 구동시키려면
`providedRuntime('org.springframework.boot:spring-boot-starter-tomcat')`을 지워서 다시 빌드하거나 tomcat을 설치해주면 된다.  
