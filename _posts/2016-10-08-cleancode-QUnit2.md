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

```html
[...]

<script>
    $(document).ready(function(){
        var loginLink = document.getElementById('id_login');
        if(loginLink){
            loginLink.onclick = function(){ navigator.id.request(); }
        }
        var logoutLink = document.getElementById('id_logout');
        if(logoutLink){
            logoutLink.onclick = function(){ navigator.id.logout(); }
        }

[...]

<div class="navbar">
    {% if user.email %}
        <p>로그인 상태 {{ user.email }}</p>
        <p><a id="id_logout" href="{% url 'logout' %}" >로그아웃</a></p>
    {% else %}
        <a href="#" id="id_login">로그인</a>
    {% endif %}
    <p>사용자 : {{ user }}</p>
</div>

[...]

```   

다시 FT를 실행하면 성공!  

<br>  

## P.310  

P.310 에서의 테스트는 `accounts/static/tests/tests.html` 을 웹 브라우저로 실행시킨 것이다. 책에서의 첫 번째 에러와 다르게 나의 첫 번째 에러는 `Script error.`이다. QUnit v2.x에 맞게 `accounts/static/tests/tests.html` 파일을 아래와 같이 수정한다.  

```html
<script src="../accounts.js"></script>
<script>
/* global $, test, equal, sinon, Superlists */

QUnit.test("initialize binds sign in button to navigator.id.request", function (assert) {
var requestWasCalled = false;
var mockRequestFunction = function () { requestWasCalled = true; };
var mockNavigator = {
    id: {
        request: mockRequestFunction,
    }
};

Superlists.Accounts.initialize(mockNavigator);

$('#id_login').trigger('click');

assert.equal(requestWasCalled, true);
});

</script>
```

![spike_login](/images/cleancode15/p.310-2.png)  

이제 책과 에러 메세지가 동일해졌다.  

그 다음 `accounts/static/accounts.js` 파일에 `window.Superlists = null;` 을 추가해서 테스트를 해본다.

![spike_login](/images/cleancode15/p.310-2.png)   

결과가 다르게 나오지만 계속해서 다음 내용을 진행해보면

```html
window.Superlists = {
	Accounts: {}
}
```  

![spike_login](/images/cleancode15/p.310-3.png)  

결과가 같다. 책을 따라서 계속 진행하면 된다.  

<br>  

## P.316  

P.316 이후로는 QUint을 사용 단위테스트를 진행하는 내용이 대부분이다. 이 전에 해왔던 것과 마찬가지로 `QUnit.module()`과 `QUnit.test()` 를 사용하면 된다.  
15장까지 작성된 `accounts/static/tests/tests.html` 의 결과이다.  

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <title>Javascript tests</title>
    <link rel="stylesheet" href="../../../superlists/static/tests/qunit.css">
</head>

<body>
    <div id="qunit"></div>
    <div id="qunit-fixture">
        <a id="id_login">로그인</a>
    </div>

    <script src="http://code.jquery.com/jquery.min.js"></script>
    <script src="../../../superlists/static/tests/qunit.js"></script>
    <script src="../../../superlists/static/tests/sinon.js"></script>
    <script src="../accounts.js"></script>
    <script>
    /* global $, test, equal, sinon, Superlists */
QUnit.test("initialize binds sign in button to navigator.id.request", function (assert) {
    var requestWasCalled = false;
    var mockRequestFunction = function () { requestWasCalled = true; };
    var mockNavigator = {
        id: {
            request: mockRequestFunction,
            watch: function () {}
        }
    };

    Superlists.Accounts.initialize(mockNavigator);
    assert.equal(requestWasCalled, false, '클릭 전에 request가 호출되지 않는지 확인');

    $('#id_login').trigger('click');
    assert.equal(requestWasCalled, true, '클릭 후에 request가 호출되는 것을 확인');
});
var user, token, urls, mockNavigator, requests, xhr;
QUnit.module("navigator.id.watch tests", {
    beforeEach: function() {
        user = '현재 사용자';
        token = 'csrf token';
        urls = { login: 'login url', logout: 'logout url' };
        mockNavigator = {
            id: {
                watch: sinon.mock()
            }
        };
        xhr = sinon.useFakeXMLHttpRequest();
        requests = [];
        xhr.onCreate = function (request) { requests.push(request); };
    },
    afterEach: function () {
        mockNavigator.id.watch.reset();
        xhr.restore();
    }
});
QUnit.test("initialize가 navigator.id.watch를 호출", function (assert){
    Superlists.Accounts.initialize(mockNavigator, user, token, urls);
    assert.equal(
        mockNavigator.id.watch.calledOnce,
        true,
        'watch 함수가 호출되는지 확인'
    );
});
QUnit.test("watch가 현재 사용자를 확인", function(assert) {
    Superlists.Accounts.initialize(mockNavigator, user, token, urls);
    var watchCallArgs = mockNavigator.id.watch.firstCall.args[0];
    assert.equal(watchCallArgs.loggedInUser, user, '사용자 확인');
});
QUnit.test("onLogin이 로그인 url에 대해 ajax post함", function(assert) {
    Superlists.Accounts.initialize(mockNavigator, user, token, urls);
    var onloginCallback = mockNavigator.id.watch.firstCall.args[0].onlogin;
    onloginCallback();
    assert.equal(requests.length, 1, 'ajax 요청 확인');
    assert.equal(requests[0].method, 'POST')
    assert.equal(requests[0].url, urls.login, 'url 확인');
});
QUnit.test("onLogin이 csrf token와 함께 어설션 전송", function(assert) {
    Superlists.Accounts.initialize(mockNavigator, user, token, urls);
    var onloginCallback = mockNavigator.id.watch.firstCall.args[0].onlogin;
    var assertion = 'browser-id assertion';
    onloginCallback(assertion);
    assert.equal(
        requests[0].requestBody,
        $.param({ assertion: assertion, csrfmiddlewaretoken: token }),
        'POST data 확인'
    );
});
QUnit.test("onlogout은 단지 함수 틀이다", function(assert) {
    Superlists.Accounts.initialize(mockNavigator, user, token, urls);
    var onlogoutCallback = mockNavigator.id.watch.firstCall.args[0].onlogout;
    assert.equal(typeof onlogoutCallback, "function", "onlogout는 함수여야 한다");
});
QUnit.test("onlog post가 실패하면 navigator.id.logout 실행", function(assert) {
    mockNavigator.id.logout = sinon.mock();
    Superlists.Accounts.initialize(mockNavigator, user, token, urls);
    var onloginCallback = mockNavigator.id.watch.firstCall.args[0].onlogin;
    var server = sinon.fakeServer.create();
    server.respondWith([403, {}, "접속 거부"]);
    onloginCallback();
    assert.equal(mockNavigator.id.logout.called, false, '아직 로그아웃하면 안 된다');
    server.respond();
    assert.equal(mockNavigator.id.logout.called, true, 'logout을 호출해야 한다');
});
    </script>


</body>
</html>
```
