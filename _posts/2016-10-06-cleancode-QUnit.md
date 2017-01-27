---
layout: post
title:  "클린 코드를 위한 테스트 주도 개발 13 - QUnit 버전 문제"
tags:   [Test, JavaScript, Django]
---

<br>  

> '클린 코드를 위한 테스트 주도 개발 - 해리 J.W. 퍼시벌' 이라는 책은 Python Django와 TDD를 이용하여 예제를 작성해보면서 웹 프로그래밍 전반과 TDD 적용 방법에 대해 설명하고 있다. TDD와 Django에 대한 기초뿐만 아니라 실제 프로젝트에서도 적용할 수 있는 다양한 기술을 경험해볼 수 있는 유익한 책이라 생각된다.  

> 책 중반부로 가면 13장에서 약간의 javascript가 필요해지면서 javascript를 테스트하기 위한 툴도 필요하게 된다. QUnit이라는 테스트 툴을 사용하는데 책을 보고 그대로 따라하면 버전 문제로 인해 동작하지 않는다. (책에서는 v1.12 를 사용하고 현재는 v2.0.1 이다.) 이 포스팅은 최신의 QUnit 버전을 이용하여 책의 예제를 진행하는데 도움을 줄 수 있을 것이다.  

<br>  

## QUnit Version Issue   

QUnit이 2.x 버전으로 Upgrade 하면서 크게 변동된 사항이 있다. 1.x에서는 많은 globals 변수들을 제공했지만 2.x 버전 부터는 `QUnit` 이라는 global 변수 하나만 제공하는 것으로 바뀌었다. `(예를 들면, Global 'test()' method 는 제거되고, 대신에 'QUnit.test()' 을 사용.)`  자세한 내용은 아래의 포스팅 혹은 공식 홈페이지 문서를 참고하면 된다.   

- <https://cjh5414.github.io/QUnit/>  

- <http://qunitjs.com/upgrade-guide-2.x/>   

<br>  

## P.269 - P.271  

우선 책에 예제를 그대로 따라 해 본다.  
[여기](http://qunitjs.com/)의 우측 Download에서 최신 버전의 QUnit .css 와 .js를 다운 받아서 아래와 같이 저장한다.

```
$ tree lists/static/tests/
lists/static/tests/
├── qunit.css
├── qunit.js
└── tests.html
```  

<br>  

_tests.html_ 을 작성한다.  

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <title>Javascript tests</title>
    <link rel="stylesheet" href="qunit.css">
</head>

<body>
    <div id="qunit"></div>
    <div id="qunit-fixture"></div>
    <script src="qunit.js"></script>
    <script>
/*global $, test, equal */
test("스모크 테스트", function () {
    equal(1, 1, "계산 성공!");
});
    </script>

</body>
</html>
```  

<br>  

웹 브라우저로 _tests.html_ 을 열어보면 책과는 다른 테스트 결과가 나온다.  
`global failure` `Script error.`  

![P.271](/images/cleancode13/p.271.png)  

<br>  

v2.x 부터는 `test()` 대신에 `QUnit.test()`를 사용하고, `function()` 매개변수로 assert를 넘겨주어서 `assert.equal()`을 사용해야한다. _tests.html_ 을 아래와 같이 수정한다.  

```html
[...]

<script src="qunit.js"></script>
<script>
/*global $, test, equal */
QUnit.test("스모크 테스트", function (assert) {
assert.equal(1, 1, "계산 성공!");
});
</script>

[...]
```  

웹 브라우저를 새로 고침하면 테스트가 통과하는 것을 볼 수 있다.

![P.271 pass](/images/cleancode13/p.271-pass.png)   

<br>  

## P.272 - P.273  

마찬가지로 QUnit 을 사용하여 수정 해주면 된다.  

before :  

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <title>Javascript tests</title>
    <link rel="stylesheet" href="qunit.css">
</head>

<body>
    <div id="qunit"></div>
    <div id="qunit-fixture"></div>

    <form>
        <input name="text" />
        <div class="has-error">에러 텍스트</div>
    </form>

    <script src="http://code.jquery.com/jquery.min.js"></script>
    <script src="qunit.js"></script>
    <script>
/*global $, test, equal */

test("스모크 테스트", function () {
    equal($('.has-error').is(':visible'), true);
    $('.has-error').hide();
    equal($('.has-error').is(':visible'), false);
});
    </script>

</body>
</html>
```  

after :  

```html
[...]

    <script>
/*global $, test, equal */

QUnit.test("스모크 테스트", function (assert) {
    assert.equal($('.has-error').is(':visible'), true);
    $('.has-error').hide();
    assert.equal($('.has-error').is(':visible'), false);
});
    </script>

[...]
```  

result :  

![P.271 pass](/images/cleancode13/p.271-pass.png)     

<br>  

## P.273 - P.274  

앞의 방식과 동일하게 수정하면 된다.    

after :  

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <title>Javascript tests</title>
    <link rel="stylesheet" href="qunit.css">
</head>

<body>
    <div id="qunit"></div>
    <div id="qunit-fixture">
        <form>
            <input name="text" />
            <div class="has-error">에러 텍스트</div>
        </form>
    </div>

    <script src="http://code.jquery.com/jquery.min.js"></script>
    <script src="qunit.js"></script>
    <script>
/*global $, test, equal */

QUnit.test("스모크 테스트", function (assert) {
    assert.equal($('.has-error').is(':visible'), true);
    $('.has-error').hide();
    assert.equal($('.has-error').is(':visible'), false);
});
QUnit.test("스모크 테스트2", function (assert) {
    assert.equal($('.has-error').is(':visible'), true);
    $('.has-error').hide();
    assert.equal($('.has-error').is(':visible'), false);
});

    </script>

</body>
</html>
```   

<br>  

## P.275   

여태까지의 내용은 책에서 QUnit의 사용법을 익히기 위해 작성한 코드였다.  
이번 13장에서 실제로 사용하는 테스트 코드도 `QUnit.test()`를 사용하면 문제없이 진행된다.  

_lists/static/tests/tests.html_  

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <title>Javascript tests</title>
    <link rel="stylesheet" href="qunit.css">
</head>

<body>
    <div id="qunit"></div>
    <div id="qunit-fixture">
        <form>
            <input name="text" />
            <div class="has-error">Error text</div>
        </form>
    </div>

    <script src="http://code.jquery.com/jquery.min.js"></script>
    <script src="qunit.js"></script>
    <script>
/*global $, test, equal */
QUnit.test("키 입력 시에 에러가 숨겨져야 한다", function (assert) {
    $('input').trigger('keypress');
    assert.equal($('.has-error').is(':visible'), false);
});
    </script>

</body>
</html>
```   

테스트 결과를 확인해 보면 책과 일치한다.  

![P.275](/images/cleancode13/p.275.png)     

이후의 내용도 `QUnit.test()`을 사용하면 문제없이 진행된다.  

QUnit에 대해 더 자세히 살펴보고 싶다면 [여기](https://cjh5414.github.io/QUnit/)를 참고하면 된다.  
