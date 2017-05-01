---
layout: post
title:  React Google Analytics
tags:   ['React']
---

> [react-ga](https://github.com/react-ga/react-ga)를 참고하여 react 프로젝트에 google analytics를 붙여서 페이지와 특정 버튼을 트래킹하는 방법에 대해서 알아본다.  

<br/>  

### 설치  

```
$ npm install react-ga --save
```  

<br/>  

### 사용법  

`react-router`를 이용하여 Google Analytics를 초기화하고 Pageviews를 트래킹 하기위한 코드를 _index.js_ 파일에 추가한다.  

_index.js_  

```
var React = require('react');
var ReactDOM = require('react-dom');
var Router = require('react-router');
var routes = require('./routes');

var ReactGA = require('react-ga');
ReactGA.initialize('UA-000000-00');

function logPageView() {
  ReactGA.set({ page: window.location.pathname });
  ReactGA.pageview(window.location.pathname);
}

var app = document.getElementById('app');
ReactDOM.render(<Router routes={routes} onUpdate={logPageView} />, app);
```  

[Google Analytics](www.google.com/analytics) 에서 계정을 만들고 생성된 추적 ID를
`UA-000000-00` 위치에 입력하면 된다.   

<br/>

### 페이지 트래킹  

Google 로그인을 한 후 <https://analytics.google.com/analytics/web/> 에 접속해보면 아래와 같이 페이지 트래킹 정보들을 확인할 수 있다.  

![pageview Tracking](/images/react-google-analytics/pageview.png)  

<br/>  

### 버튼 트래킹  

특정 버튼에 대한 트래킹을 하고 싶다면 아래와 같이 원하는 버튼의 `onClick`에 ga 코드를 추가해주면 된다.  

```
ReactGA.ga('send', 'event', 'event_category', 'event_action', 'event_label');
```  

이벤트 카테고리, 액션, 라벨 명을 지정해놓으면 트래킹 결과를 확인할 때 더 편리하게 사용할 수 있다.  

![Button Tracking](/images/react-google-analytics/button.png)  
