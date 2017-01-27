---
layout: post
title: 진행 중인 프로젝트에 travis-ci 붙이기
tags:  [Test, TravisCI]
---

<br>  

현재 '클린 코드를 위한 테스트 주도 개발'이라는 책을 보면서 To-Do list 프로젝트를 따라해보고 있다.
git으로 관리하는 이 프로젝트에 travis-ci를 붙여보고자 한다.  
<https://docs.travis-ci.com/user/getting-started/> 여기에 가면 사용법이 나와있다.


## .travis.yml 추가  

사용되는 언어, 버전, 스크립트 등의 내용을 작성한다.  
python은 <https://docs.travis-ci.com/user/languages/python/> 를 참고하면 된다.

```
language: python
python:
  - "3.5"
# command to install dependencies
install: "sudo pip install -r requirements.txt"
# command to run tests
script: python manage.py test lists/
sudo: required
```   

## requirements.txt 만들기  

```
$ pip freeze > requirements.txt
```  

## 해당 프로젝트 travis-ci 설정  

<https://travis-ci.org/profile> 에 접속하여 해당 프로젝트에 체크표시를 한다.    

![travis-ci](/images/travis-ci/project-travis.png)

## 변경사항 commit  

```
git add .travis.yml
git add requirements.txt
git commit -m "add .travis.yml and requirements.txt"
git push origin master
```

## 확인  
<https://travis-ci.org/> 에 접속하여 결과를 확인한다.  
git push를 하면 travis-ci가 알아서 테스트를 진행한다.   


![travis-ci](/images/travis-ci/project-travis-result.png)

결과는 메일로도 발송된다.  

![travis-ci](/images/travis-ci/project-travis-email.png)
