---
layout: post
title:  "form 유효성 검사 비활성화 - novalidate"
date:   2016-11-01
categories: html
---

<br>  

form에 `input type="file"`을 이용하여 file upload 기능을 구현하는 중에 아래와 같은 문제가 발생하였다.  

![novalidate](/images/novalidate/novalidate.png)  

파일을 추가하지 않으면 경고창이 뜨고 form이 제출되지 않아서 다음 단계로 진행되지가 않는다.  

form 제출 시에 유효성 검사를 하는데 통과하지 못하면 경고창을 띄운다. 유효성 검사를 하지않게 하는 방법은 form에 novalidate 속성을 추가하면 된다.   

```html
<form method="post" action="/" novalidate>
```  

file 누락 유효성 검사 이외에도 입력란이 비어 있는지 등의 다른 유효성 검사들도 이 방법으로 검사하지 않을 수 있다.  
