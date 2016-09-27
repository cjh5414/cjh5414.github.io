---
title: Github Pages 기능 이용하기
layout: post
---

## Github의 Pages란?
Github에서 무료로 호스팅하는 공개 웹페이지이다. 웹 서버를 구축하지 않고도 github의 repository에 index.html 파일을 넣는 것 만으로 간편하게 개인 사이트를 만들 수 있다.

## Github Pages 만들기  

### 1. Create a Repository  
github에 로그인을 하고 새로운 repository를 생성한다.  
repository의 이름은 반드시 _[username]_.github.io 로 지정해야한다.  
![New Repository](/images/githubpages/githubpages_new.png)  

### 2. Clone the repository  
terminal을 실행시키고 project를 clone 하고자 하는 폴더로 이동한 후 다음 명령을 실행한다.  

```
$ git clone https://github.com/_[username]_/_[username]_.github.io
```

### 3. Create an index.html  
clone한 project 폴더로 이동한다.  


```
$ cd _[username]_.github.io
```
개인 사이트에 보여질 index.html을 작성한다.  

```
$ echo "Hello World" > index.html
```

### 4. Push  
작성한 index.html을 commit, push한다.  

```
$ git add --all  
$ git commit -m "Initialize Github Pages"  
$ git push -u origin master  
```

https://_[username]_.github.io 주소로 확인한다.  
![New Repository](/images/githubpages/githubpages_result.png)  

### 5. 참고 자료  
<https://pages.github.com>



-----------------------