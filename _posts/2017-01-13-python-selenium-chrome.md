---
layout: post
title:  "Selenium with python, chrome, pytest"
categories: python
---

<br/>  

## Install Selenium, pytest

```
$ pip install selenium
$ pip install pytest
```  

<br/>  

## chromedriver setting  

[chromedriver download](https://sites.google.com/a/chromium.org/chromedriver/downloads) 에서 chromedriver를 다운받는다.  


<br/>  

## Simple Usage  

_selenium-test.py_  

```python  
from selenium import webdriver

driver = webdriver.Chrome('chromedriver PATH')
driver.get("https://cjh5414.github.io")
driver.quit()
```  

chromedriver PATH 에는 _/usr/local/bin/chromedriver_ 와 같이 다운받은 chromedriver가 실제로 위치한 경로를 입력해준다.  

<br/>  

## Result  

```
$ python selenium-test.py
```  

코드를 돌려보면 driver.get()에서 지정한 url로 브라우저가 실행됐다가 종료되는 것을 확인할 수 있다.   

<br/>  

## pytest  

selenium을 통해 chrome browser 에서 `https://cjh5414.github.io` 으로 접속했을 때 title이 `jihun's Development blog •`과 같은지 확인해보는 테스트코드로 살짝 변경해보았다.  

```python
from selenium import webdriver

def test_selenium():
    driver = webdriver.Chrome('chromedriver')
    driver.get("https://cjh5414.github.io")
    assert driver.title == "jihun's Development blog •"
    driver.quit()
```   

pytest 는 test로 시작하는 메소드를 찾아서 테스트를 실행시켜 준다.  
`pytest selenium-test.py`의 명령으로 결과를 확인해보면 아래와 같다.  

![pytest result](/images/python-selenium/pytest result.png)  
