---
layout: post
title:  "[Mac] Spring - Eclipse, Tomcat, MySQL"
date:   2016-12-20
categories: python
---

<br>  

## Java Install  

> Eclipse를 설치하기 전에 Java JDK를 먼저 설치해야한다. JDK를 설치하면 JRE도 같이 설치된다.    

[JDK Download](https://java.com/ko/download/mac_download.jsp)
에서 설치 프로그램을 다운받아서 실행한다.   
CLI로 java를 실행시켰을 때 아래와 같이 나오면 설치가 잘 된 것이다.  

```
$ java -version
java version "1.8.0_111"
Java(TM) SE Runtime Environment (build 1.8.0_111-b14)
Java HotSpot(TM) 64-Bit Server VM (build 25.111-b14, mixed mode)
```  

<br>  

## Eclipse Install  

[Eclipse Download](http://www.eclipse.org/downloads/) 에서 최신 버전의 Eclipse를 다운받는다. 압축을 풀고 설치를 진행하면 된다.  

<br>  

## Tomcat Install  

[Tomcat](http://tomcat.apache.org/) 에서 좌측 Download에 원하는 버젼을 선택한다.  
Core의 tar.gz을 선택하여 다운로드하고 압축을 푼다.  

![Tomcat Download](/images/mac-spring-setting/tomcat-download.png)  

<br>  

> 아래의 과정은 압축을 풀어서 생성된 폴더의 위치를 변경하고 심볼릭 링크를 설정함으로써 tomcat에 쉽게 접근하기 위함이다. 후추에 tomcat의 버전을 변경할 일이 있으면 대체가 용이해진다.  

__1. terminal에서 아래의 명령을 실행하여 tomcat 폴더의 위치를 변경한다.(/user/local 폴더가 없다면 만들어준다.)__   

```
$ sudo mkdir -p /usr/local
$ sudo mv ~/Downloads/apache-tomcat-8.0.39 /usr/local/
```  

__2. Tomcat에 접근하기 쉽도록 심볼릭 링크를 설정한다.(기존의 Tomcat 폴더가 이미 존재한다면 지우고 다시 만든다.)__  

```
$ sudo rm -f /Library/Tomcat
$ sudo ln -s /usr/local/apache-tomcat-8.0.39 /Library/Tomcat
```  

__3. 파일의 소유자를 바꾼다.__   

```
$ sudo chown <user> /Library/Tomcat
```   

__4. Tomcat/bin의 모든 script를 실행가능하도록 한다.__  

```
$ sudo chmod +x /Library/Tomcat/bin/*.sh
```  

<br>  

### Tomcat Install 확인  

```
$ /Library/Tomcat/bin/startup.sh
Using CATALINA_BASE:   /Library/Tomcat
Using CATALINA_HOME:   /Library/Tomcat
Using CATALINA_TMPDIR: /Library/Tomcat/temp
Using JRE_HOME:        /Library/Java/JavaVirtualMachines/jdk1.8.0_111.jdk/Contents/Home
Using CLASSPATH:       /Library/Tomcat/bin/bootstrap.jar:/Library/Tomcat/bin/tomcat-juli.jar
Tomcat started.
```  

위와 같이 startup.sh을 실행 후 웹 브라우저에 `http://localhost:8080/`을 입력하여 아래의 결과가 나오면 정상작동 하는 것이다.  

![Tomcat Test](/images/mac-spring-setting/tomcat-test.png)  

<br>  

종료하는 방법은 아래와 같다.  

```
$ /Library/Tomcat/bin/shutdown.sh
Using CATALINA_BASE:   /Library/Tomcat
Using CATALINA_HOME:   /Library/Tomcat
Using CATALINA_TMPDIR: /Library/Tomcat/temp
Using JRE_HOME:        /Library/Java/JavaVirtualMachines/jdk1.8.0_111.jdk/Contents/Home
Using CLASSPATH:       /Library/Tomcat/bin/bootstrap.jar:/Library/Tomcat/bin/tomcat-juli.jar
```  

<br>  

## MySQL Install   

> HomeBrew를 통한 설치방법 입니다.  

__1. mysql 설치__  

```
$ brew install mysql
```  

__2. mysql 시작__  

```
$ mysql.server start
Starting MySQL
. SUCCESS!
```  

<br>  

## 참고자료  

- <http://nillk.tistory.com/>  

- <https://wolfpaulus.com/journal/mac/tomcat8/>  
