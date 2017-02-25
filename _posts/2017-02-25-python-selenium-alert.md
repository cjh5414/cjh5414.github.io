---
layout: post
title:  Python Selenium Alert창 다루기
tags:   [Python]
---

> Selenium을 이용하여 기능테스트를 작성하는 중에 alert 창에 대해 테스트를 할 일이 생겼다. alert창을 테스트하기 위해서는 `driver.switch_to_alert()` 또는 `Alert(driver)` 을 이용하면 된다. selenium에서 alert 객체를 통해 alert의 text가져오기, text입력하기, 확인, 취소 등의 기능을 이용할 수 있다.  

<br/>  

## driver.switch_to_alert()  

`driver.switch_to_alert()`을 호출하면 alert에 대한 객체를 넘겨준다.  

```python
from selenium import webdriver


driver = webdriver.Firefox()
driver.get("http://localhost:8000")

[...]

alert = driver.switch_to_alert()
```  

- __alert text 얻기__  

```python
assert "alert창" in alert.text
```  

- __alert 확인__  

```python
alert.accept()
```

- __alert 취소__  

```python
alert.dismiss()
```

<br/>  

## Alert(driver)  

`driver.switch_to_alert()` 을 사용하는 대신 `Alert`를 import 해주고 `Alert(driver)`을 사용하는 것 외에는 동일하다.   

```python
from selenium import webdriver
from selenium.webdriver.common.alert import Alert

driver = webdriver.Firefox()
driver.get("http://localhost:8000")

[...]
```  

- __alert text 얻기__  

```python
assert "alert창" in Alert(driver).text
```  

- __alert 확인__  

```python
Alert(driver).accept()
```

- __alert 취소__  

```python
Alert(driver).dismiss()
```

<br/>  

## 참고자료  

- <http://selenium-python.readthedocs.io/navigating.html?highlight=alert>  

- <http://selenium-python.readthedocs.io/api.html#module-selenium.webdriver.common.alert>
