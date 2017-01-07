---
layout: post
title:  "CircleCI와 Heroku를 이용하여 배포 자동화 하기"
date:   2017-1-7
categories: CI
---

> 매일 오전 학식 메뉴를 Notification 해주는 간단한 프로그램을 만들었었다. 개발할 당시 코드는 github repository으로도 관리를 하고 heroku 배포를 위해 heroku repository에서도 관리를 했었다. 소스코드를 수정하면 github와 heroku repository 두 가지 모두에 push를 해줘야 했었다. 최근 CircleCI를 간단하게 사용해볼 경험이 있었는데 이 프로젝트에 CircleCI heroku 배포 자동화를 적용해보면 좋을 것 같다는 생각이 들었다. CircleCI을 사용하여 배포 자동화를 하면 github에 push를 할 때마다 알아서 빌드를 하고 배포까지 자동화를 할 수 있기 때문에 매우 편리하다.  

<br/>  

## Heroku & CircleCI Deployment Setting

### 1. Heroku API key  

[Heroku account](https://dashboard.heroku.com/account) 에 가서 API key를 복사한다.  

![Heroku CircleCI Deployment](/images/heroku-circleci-deployment/heroku api.png)  

<br/>   

[Heroku Dashboard](https://circleci.com/dashboard)에서 배포 자동화를 적용할 프로젝트의 설정 페이지로 이동한다. (톱니바퀴 모양의 버튼을 클릭하면 된다.)  
좌측의 리스트 중에 `Heroku Depoyment`를 클릭한다.  
`Step 1: Heroku API key` 의 `Add new key`입력란에 복사해뒀던 API key를 넣고 `Save Heroku Key` 버튼을 클릭한다.  


![Insert api key in circleci](/images/heroku-circleci-deployment/insert api in circleci.png)  

<br/>  

### 2. Associate a Heroku SSH key with your account  

`Step 2: Associate a Heroku SSH key with your account` 에서 `Set User to [username]` 버튼을 클릭한다.  

![Herkou SSH key](/images/heroku-circleci-deployment/heroku SSH key.png)  

<br/>  

### 3. Add deployment settings to your circle.yml file  

배포 자동화를 위한 설정을 _circle.yml_ 파일을 통해 해준다.  
아래와 같이 branch와 herkou의 appname을 입력해준다.  

github에 push가 되면 CircleCI는 _circle.yml_ 파일에 있는 설정들을 바탕으로 알아서 빌드를 수행한다. 아래의 내용과 위에서 설정한 Heroku의 정보들을 이용하여 heroku의 appname으로 배포를 수행한다.  

_circle.yml_  

```

[...]

deployment:
    production:
        branch: master
        heroku:
            appname: hansung-bab
```  

수정한 _circle.yml_ 을 CircleCI에서 적용할 수 있도록 push 해줘야 한다.  

```
$ git add circle.yml
$ git commit -m "Add heroku deployment setting on circle.yml"
$ git push
```  

수정한 코드를 push하면 빌드를 알아서 수행한다.  


<br/>  

## Deployment Result  

빌드 성공했고 결과를 보면 DEPLOYMENT 부분이 추가된 것을 확인할 수 있었다.  

![Herkou Build Result](/images/heroku-circleci-deployment/build result.png)   

Deploy to hansung-bab 부분을 자세히 보면 Heroku에 배포 되었다는 log 메세지가 보인다.  

![Herkou Build Result Detail](/images/heroku-circleci-deployment/build result detail.png)  

Heroku에서도 확인해 보면 빌드가 성공하고 배포되었다는 메세지를 확인할 수 있다.  

![Herkou Deployment Result](/images/heroku-circleci-deployment/deployment result.png)  
