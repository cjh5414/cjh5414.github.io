---
layout: post
title:  "Selenium with python, chrome"
date:   2017-01-13
categories: python
---

<br/>  

## Install Selenium  

```
$ pip install selenium
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
