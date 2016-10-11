---
layout: post
title:  "클린 코드를 위한 테스트 주도 개발 15 - QUnit 버전 문제 2 + 스파이킹 문제"
date:   2016-10-08
categories: test
comments: true
---

<br>  

> [지난 포스팅](https://cjh5414.github.io/cleancode-QUnit/)에 이어 ‘클린 코드를 위한 테스트 주도 개발 - 해리 J.W. 퍼시벌’ 15장의 예제를 QUnit v2.0.1 을 사용하면서 진행해봤다. 또한 스파이크(Spike) 예제를 진행하는 과정에서 발생하는 문제도 몇 가지 있어서 해결법을 공유한다.

<br>  

## QUnit Version Issue   

QUnit이 2.x 버전으로 Upgrade 하면서 크게 변동된 사항이 있다. 1.x에서는 많은 globals 변수들을 제공했지만 2.x 버전 부터는 `QUnit` 이라는 global 변수 하나만 제공하는 것으로 바뀌었다. `(예를 들면, Global 'test()' method 는 제거되고, 대신에 'QUnit.test()' 을 사용.)`  자세한 내용은 아래의 포스팅 혹은 공식 홈페이지 문서를 참고하면 된다.   

- <https://cjh5414.github.io/QUnit/>  

- <http://qunitjs.com/upgrade-guide-2.x/>   

<br>  

## P.293 - P.279  

먼저 스파이크 예제에서 발생하는 문제이다.  
297 페이지까지 진행한 후에 runserver로 개발 서버를 실행해보면 아래와 같은 에러 메세지가 뜬다.

```
File "[...]/superlists/accounts/authentication.py", line 1, in <module>
  import requests
ImportError: No module named 'requests'
```

P.293 을 자세히 보면 'accounts/authentication.py'에서 `import requests` 을 사용하고 있는데 아래의 설명은 `pip install request`을 하라고 나와있다. `pip install requests`을 해보고 다시 runserver를 실행해보자.  

![spike](/images/cleancode15/spike.png)  

![spike_login](/images/cleancode15/spike_login.png)  

![spike_login](/images/cleancode15/spike_logout.png)  

정삭적으로 동작한다. 하지만 우리의 결과 화면과는 다르게 P.297 '그림 15-1'에는 부트스트랩이 적용되어 있다. P.304 으로 잠깐 넘어가 보면 부트스트랩을 적용하는 과정이 나와있다. 책의 그림을 삽입하는 과정에서 실수가 있었던 것 같다. 지금 적용할 필요는 없고 그냥 다음으로 넘어가면 된다.

<br>  

## P.303   

FT를 실행하면 성공하는 것을 알 수 없다. 실패한다.

```
$ python3 manage.py test functional_tests.test_login
======================================================================
ERROR: test_login_with_persona (functional_tests.test_login.LoginTest)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "/Users/jihun/Development/TDD_django_cleancode/superlists/functional_tests/test_login.py", line 28, in test_login_with_persona
    self.browser.find_element_by_id('id_login').click()

[...]

selenium.common.exceptions.NoSuchElementException: Message: no such element: Unable to locate element: {"method":"id","selector":"id_login"}
```   

`id_login`을 찾지 못하고있다. `lists/templates/base.html`을 열어서 확인해보니 id를 `login`과 `logout`을 사용하고 있다.
해당 부분들을 찾아서 `id_login`과 `id_logout`으로 바꿔주자. (뒤에서 id_xxx를 계속 사용한다.)  

```js
$(document).ready(function(){
    var loginLink = document.getElementById('id_login');
    if(loginLink){
        loginLink.onclick = function(){ navigator.id.request(); }
    }
    var logoutLink = document.getElementById('id_logout');
    if(logoutLink){
        logoutLink.onclick = function(){ navigator.id.logout(); }
    }


});
```  

```
<div class="navbar">
    {% if user.email %}
        <p>로그인 상태 {{ user.email }}</p>
        <p><a id="id_logout" href="{% url 'logout' %}" >로그아웃</a></p>
    {% else %}
        <a href="#" id="id_login">로그인</a>
    {% endif %}
    <p>사용자 : {{ user }}</p>
</div>
```    
