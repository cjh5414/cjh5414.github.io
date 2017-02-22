---
layout: post
title:  Python, Selenium, ChromeDriver 예제코드와 importError 해결 방법
tags:   [Python, Test]
---

> Python 3.6, Selenium 3.0.2, ChromeDriver 2.27 (현재 시점에서 모두 최신 버전.)을 사용하여 python 코드를 작성하여 실행하였더니 "ImportError: sys.meta_path is None, Python is likely shutting down" 라는 오류가 발생했다.  

## Settings  

- Selenium Install : `$ pip install selenium`  
- [ChromeDriver download](https://sites.google.com/a/chromium.org/chromedriver/downloads)  

<br/>  

## Example code  

python 예제 코드와 chromedriver 파일이 동일한 위치에 있다.  

```
$ tree .
.
├── chromedriver
├── test.py

[...]
```   


_test.py_  

```python
import os
from selenium import webdriver

chrome_path = os.path.join(os.path.dirname(os.path.realpath(__file__)), 'chromedriver')
browser = webdriver.Chrome(chrome_path)

browser.get('http://www.google.com')
```  

위의 코드를 실행시켜보면 오류 메세지가 아래와 같이 나온다.   

```
$ python3 test.py
Exception ignored in: <bound method Service.__del__ of <selenium.webdriver.chrome.service.Service object at 0x1098aceb8>>
Traceback (most recent call last):
  File "/Users/jihun/.pyenv/versions/clean_code/lib/python3.6/site-packages/selenium/webdriver/common/service.py", line 173, in __del__
  File "/Users/jihun/.pyenv/versions/clean_code/lib/python3.6/site-packages/selenium/webdriver/common/service.py", line 149, in stop
  File "/Users/jihun/.pyenv/versions/clean_code/lib/python3.6/site-packages/selenium/webdriver/common/service.py", line 120, in send_remote_shutdown_command
ImportError: sys.meta_path is None, Python is likely shutting down
```   

browser을 close 하지 않아서 그런듯하여 `browser.close()` 코드를 추가해봤지만 동일한 오류가 발생했다. 구글링을 해보니 해결 방법을 쉽게 찾을 수 있었다.  

<br/>  

## 해결방법  

`browser.close()` 대신에 `browser.quit()` 을 사용하면 바로 해결된다.    

_test.py_  

```python
import os
from selenium import webdriver

chrome_path = os.path.join(os.path.dirname(os.path.realpath(__file__)), 'chromedriver')
browser = webdriver.Chrome(chrome_path)

browser.get('http://www.google.com')
browser.quit()
```  

<br/>  

## 참고자료  

<http://stackoverflow.com/questions/41480148/importerror-sys-meta-path-is-none-python-is-likely-shutting-down>
