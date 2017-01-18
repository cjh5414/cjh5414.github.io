---
layout: post
title:  "CircleCI Background"
date:   2017-01-18
categories: CI
---

> python으로 Simple Http Server를 띄우고 selenium으로 web page title을 확인해보는 간단한 테스트 코드를 CircleCi에 올렸다. 서버를 background로 실행시키도록 스크립트 명령에 &를 붙여서 사용해봤더니 title이 `Problem loading page`를 반환해서 테스트에 실패했다. 문제의 원인은 circle.yml에서 &를 사용할 수 없기 때문이었다. `background: true` 을 사용하면 해결된다. 원인을 파악하는데 시간이 꽤 오래 걸렸었는데 알고보니 circleci docs에 맨 첫줄에 답이 나와있었다. docs를 처음부터 잘 읽어봐야겠다는 필요성을 다시 한 번 느꼈다.  

<br/>  

## 문제 발생 코드  

#### _simple-test.py_   

selenium으로 web browser를 띄우고 `http://localhost:8000`으로 접속해 title을 확인하는 테스트 코드이다.  

_simple-test.py_   

```python
from selenium import webdriver

def test_selenium():
    driver = webdriver.Firefox()
    driver.get("http://localhost:8000")

    assert driver.title == 'Directory listing for /'
    driver.close()
```   

<br/>  

#### _circle.yml_  

CircleCI의 환경을 설정하는 파일로 테스트 코드를 실행하기 전에 simple 서버를 백그라운드로 띄우고자 `python -m http.server 8000` 명령에 &를 붙여보았다.  

_circle.yml_   

```
[...]

test:
  pre:
    - python -m http.server 8000 &
  override:
    - pytest selenium-browser.py
```  

<br/>  

#### Builde Result  

![Build Result](/images/circleci-background/Build Result.png)  

<br/>  
