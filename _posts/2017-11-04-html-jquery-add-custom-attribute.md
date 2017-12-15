---
layout: post
title:  HTML 태그에 커스텀 속성 추가하여 JQuery에서 사용하기
tags:   ['HTML', 'JQuery', 'JavaScript']
---

> JQuery에서 ID를 이용하여 HTML 태그의 click 기능을 구현할 때 HTML에 있는 특정 값을 가져와서 사용해야 할 경우가 있다. Django 템플릿을 이용하는 예를 들면, 템플릿에서 모델 객체의 값들(`poll.id` 와 같은)을 태그의 text 으로 보여주는 것이 아니라 JavaScript 에서 그 값을 처리하기 위해 필요한 경우 JQuery 에서 해당 값을 가져오기 위해서는 HTML 태그에 Custom 속성을 지정하는 방법이 있다.  

<br/>  

## 사용 방법  

[JQuery 문서](http://api.jquery.com/data/)에 예시가 잘 나와있다.  

```html
<div data-role="page" data-last-value="43" data-hidden="true" data-options='{"name":"John"}'></div>
```    

HTML 커스텀 속성을 지정하고자 하는 태그에 `data-name="value"` 형태로 속성 이름과 값을 지정하면,   

```javascript
$( "div" ).data( "role" ) === "page";
$( "div" ).data( "lastValue" ) === 43;
$( "div" ).data( "hidden" ) === true;
$( "div" ).data( "options" ).name === "John";
```  

JQuery를 이용하여 위와 같이 `.data()` 함수를 이용하여 편하게 사용할 수 있다.   
`.data(name)` 와 같이 호출하면 HTML에서 속성 값으로 지정한 `value`를 반환한다.  

<br/>

커스텀 속성 값을 변경하거나 제거하고 싶으면 아래와 같이 할 수 있다.  

```javascript
$( "div" ).data( "change", 86 );
$( "div" ).removeData( "blah" );
```  



