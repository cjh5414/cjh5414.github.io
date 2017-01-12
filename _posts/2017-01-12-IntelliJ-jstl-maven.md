---
layout: post
title:  "IntelliJ JSTL Maven"
date:   2017-01-12
categories: IntelliJ
---

> IntelliJ 에서 JSTL을 사용하기 위해 Maven을 이용하여 설치를 했는데  
HTTP Status 500 - The absolute uri: http://java.sun.com/jsp/jstl/core cannot be resolved in either web.xml or the jar files deployed with this application  
이라는 오류가 발생했다. Tomcat_Home/lib/ 에 jstl-1.2.jar를 추가했더니 해결이 됐다.

<br/>

## Maven Project  

프로젝트에서 Maven을 사용할 수 있도록 framework support를 추가한다.  
좌측의 Project Directory를 우클릭하여 `Add Frameworks Support`를 클릭하면 아래와 같은 화면이 나온다.  

![Maven Project](/images/intellij-jstl-maven/maven project.png)  

아래쪽의 Maven을 선택하고 `OK` 버튼을 누른다.  
Project Directory에 pom.xml이 추가된 것을 확인할 수 있다.

<br/>  

## Add Dependency to Maven pom.xml  

```xml
[...]
    <groupId>groupId</groupId>
    <artifactId>helloMVC</artifactId>
    <version>1.0-SNAPSHOT</version>

    <dependencies>
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>jstl</artifactId>
            <version>1.2</version>
        </dependency>
    </dependencies>

</project>
```  

pom.xml을 수정하면 아래 쪽에 추가한 dependency를 import 하라는 메세지가 나온다.   

![maven import](/images/intellij-jstl-maven/maven import.png)   

`Import Changes` 를 클릭 하면된다.    

![maven result](/images/intellij-jstl-maven/maven result.png)    

Project Directory를 다시 확인해보면 External Libraries 에 `Maven: javax.servlet:jstl:1.2` 가 추가된 것을 확인할 수 있다.  

<br/>  

### Exception : The absolute uri: http://java.sun.com/jstl/core cannot be resolved  

jstl 코드를 추가하여 프로젝트를 돌려보면   

HTTP Status 500 - The absolute uri: http://java.sun.com/jsp/jstl/core cannot be resolved in either web.xml or the jar files deployed with this application  

이라는 오류 메세지와 함께 Exception이 발생한다.  

![Error Message](/images/intellij-jstl-maven/error.png)    

<br/>  

## Resolve Error  

해결 방법으로는 Tomcat이 깔려있는 위치에 lib 폴더 밑에 _jstl-1.2.jar_ 파일을 추가해주면 된다.  

[jstl-1.2.jar Download](https://mvnrepository.com/artifact/javax.servlet/jstl/1.2)에 가서 다운을 받고 Tomcat이 깔려있는 위치로 이동 한 후 _Tomcat/lib/_ 으로 다운받은 파일을 옮겨주면 된다.  

```
$ cd /Library/Tomcat/lib/
$ mv /Users/jihun/Downloads/jstl-1.2.jar .
```  

![jstl jar file](/images/intellij-jstl-maven/jstl jar file.png)    
