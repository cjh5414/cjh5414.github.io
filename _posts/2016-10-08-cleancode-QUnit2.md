---
layout: post
title:  "클린 코드를 위한 테스트 주도 개발 - QUnit 버전 문제 2"
date:   2016-10-08
categories: test
comments: true
---

<br>  

> [지난 포스팅](https://cjh5414.github.io/cleancode-QUnit/)에 이어 ‘클린 코드를 위한 테스트 주도 개발 - 해리 J.W. 퍼시벌’ 15장에서 QUnit v2.0.1 을 사용하면서 발생하는 문제들을 해결해보았다.

<br>  

## QUnit Version Issue   

QUnit이 2.x 버전으로 Upgrade 하면서 크게 변동된 사항이 있다. 1.x에서는 많은 globals 변수들을 제공했지만 2.x 버전 부터는 `QUnit` 이라는 global 변수 하나만 제공하는 것으로 바뀌었다. `(예를 들면, Global 'test()' method 는 제거되고, 대신에 'QUnit.test()' 을 사용.)`  자세한 내용은 아래의 포스팅 혹은 공식 홈페이지 문서를 참고하면 된다.   

- <https://cjh5414.github.io/QUnit/>  

- <http://qunitjs.com/upgrade-guide-2.x/>   

<br>  

## P.
