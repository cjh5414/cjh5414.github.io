---
layout: post
title:  "CircleCI 에서 python 3.6.0 이용하기"
date:   2017-01-15
tags: [Python, CI, CircleCI]
---

> python 최신 버전인 3.6.0 을 사용하여 CircleCI를 붙여봤는데 오류가 발생했다. 3.6.0이 최신 버전이기 때문에 CircleCI에서 아직 지원을 안 하는듯하다. 해결방법은 CircleCI 원격 컴퓨터의 .pyenv repository를 최신버전으로 pull 받도록 circle.yml를 수정하면 된다.  

<br/>  

## Python 3.6.0 Error in CircleCI  

아래와 같이 python version 을 3.6.0 으로 설정하도록 _circle.yml_ 을 작성한 후   

_circle.yml_  

```
machine:
  python:
    version: 3.6.0
```  

push를 했더니 CI가 빌드를 수행하다가 _MACHINE_ 단계에서 아래와 같은 오류가 발생했다.  

![circleci python3.6.0 fail](/images/circleci-python3.6.0/build fail.png)   

<br/>  

## Use Python 3.6.0 in CircleCI  

_circle.yml_ 파일을 아래와 같이 변경해서 .pyenv repositofy를 git pull 하도록 한다.  

_circle.yml_  

```
machine:
  pre:
    - cd /opt/circleci/.pyenv; git pull
  python:
    version: 3.6.0
```  

push 해서 확인해보면 python 3.6.0 이 설치됐다는 메세지를 확인할 수 있고 build가 성공한다.  

![circleci python3.6.0 success](/images/circleci-python3.6.0/build success.png)   

<br/>  

## 참고자료  

<http://stackoverflow.com/questions/41381930/use-python-3-6-0-in-circleci/>  
