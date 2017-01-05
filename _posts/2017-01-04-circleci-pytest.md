---
layout: post
title:  "CircleCI 붙이기 - github, pytest"
date:   2017-1-4
categories: CI
---

<br/>  

> github으로 관리하고 있는 프로젝트에 circleci를 붙이는 방법에 대해서 알아보았다. circleci와 같은 CI 툴을 이용하면 git commit을 할때마다 알아서 build 하고 결과를 알려주기 때문에 매우 편리하고 compile, test, deployment 등을 자동화 할 수 있는 장점이 있다.  

<br/>  

## CircleCI Add projects  

[Circleci signup](https://circleci.com/signup/) 에 들어가서 github을 선택한다.   

![Circleci signup](/images/circleci-pytest/signup.png)  

![Circleci add projects](/images/circleci-pytest/add projects.png)  

1번에서 organization을 선택한다. 원하는 organization이 보이지 않는다면 [GitHub's application permissions](https://github.com/settings/connections/applications/78a2ba87f071c28e65bb)에 가서 해당 organization의 `Grant access`을 눌러주면 된다.  

그 다음 2번에서 원하는 프로젝트의 `Build project`를 선택한다.  

Build 결과는 [CircleCI Builds](https://circleci.com/dashboard) 에서 확인할 수 있다.  

<br/>  

## pytest  

![result fail](/images/circleci-pytest/result fail.png)  

project를 Circleci에 추가하면 알아서 Build를 진행한다. 성공하지만 `NO TESTS`라는 경고 메세지가 뜬다. 경고 메세지를 없애기 위해 pytest를 이용하여 간단한 테스트 코드를 추가해서 commit을 해보았다.  

우선 pytest를 설치하고 requirements.txt에 패키지 리스트를 저장한다.  

```
$ pip install pytest
$ pip freeze > requirements.txt
```  

매우 간단한 test를 작성한다.  

_test_sample.py_  

```python
def test_sample():
    assert True
```  

돌려보면

```
$ pytest test_sample.py
============================= test session starts ==============================
platform darwin -- Python 3.5.2, pytest-3.0.5, py-1.4.32, pluggy-0.4.0
rootdir: /Users/jihun/Projects/hansung-bab, inifile:
collected 1 items

test_sample.py .
```  

통과한다.
commit 을 하고 Build 결과를 확인해보면 그래도 아직 `NO TESTS`.

circleci에서 test를 실행할 수 있도록 추가적인 설정이 필요하다.  

<br/>  

## circle.yml  

CircleCI는 _circle.yml_ 파일을 통해 build 할 때 필요한 설정들을 지정할 수 있다.  
test를 실행하는 명령과 프로젝트에 필요한 패키지들을 설치하는 명령을 아래와 같은 형식으로 입력한다.  

_circle.yml_   

```
test:
    override:
        - pytest test_sample.py

dependencies:
    pre:
       -  pip install -r requirements.txt
```  

다시 commit을 해보면 경고 메세지가 사라지고 build 성공하는 것을 확인할 수 있다.  

![result success](/images/circleci-pytest/result success.png)  

[Circle CI manually](https://circleci.com/docs/manually/)에 Circleci의 구체적인 사용법에 대해 나와있으니 참고하면 된다.

<br/>  

## 참고자료  

<https://circleci.com/docs/getting-started/>  
<https://circleci.com/docs/manually/>
