---
layout: post
title:  Python 프로젝트에 Codecov 연동하기
tags:   [Python, Test]
---

> 코드 커버리지(Code Coverage)란 소프트웨어의 테스트가 얼마나 코드를 잘 커버하고 있는가를 나타내주는 지표 중 하나이다. 코드 커버리지를 측정해주는 도구들이 많이 나와있는데 그 중에 하나가 Codecov이다. 이 포스트에서는 pytest, CircleCI 를 이용하고 있는 python 프로젝트에 pytest-cov 패키지를 이용하여 Codecov를 붙여보는 방법에 대해서 설명한다. git push를 하면 CircleCI 가 알아서 빌드 하면서 pytest-cov를 실행하고 code coverage를 측정하여 알려준다.  

<br/>  

# 사전 준비  

위에서 언급했듯이 pytest-cov를 이용하기 위해서는 Test와 CI 환경이 구축되어 있어야한다. 도움이 필요하다면 아래의 링크를 참고하면 된다.  

- [CircleCI 연동하기 - github, pytest](https://cjh5414.github.io/circleci-pytest/)  
- [Travis-ci를 이용하여 python 'Hello world' 작성하기](https://cjh5414.github.io/travis-ci/)  
- [진행 중인 프로젝트에 travis-ci 붙이기](https://cjh5414.github.io/project-travis-ci/)  

<br/>  

### Codecov 시작하기   

[Codecov](https://codecov.io/) 에서 세개의 웹호스팅 서비스 중에 하나를 선택하여 로그인한다.  
이 포스트에서는 Github을 이용한다.

![codecov main](/images/codecov/codecov main.png)  

해당하는 organization의 `Integrate a repository`을 누르면 다음 페이지로 넘어가고 `Add new repository` 버튼을 통해 프로젝트를 선택할 수 있다.  

![codecov gh](/images/codecov/codecov gh.png)   

아래와 같은 그림이 나오면 프로젝트 설정이 완료된 것이다. Upload Token을 제공해 주는데 public 프로젝트에서는 필요없다.  

![codecov complete](/images/codecov/complete setting.png)   

<br/>  

# Install pytest-cov  

패키지들을 설치하고 requirements를 업데이트 한다.  

```
$ pip install codecov
$ pip install pytest-cov
$ pip freeze > requirements.txt
```  

<br/>  

# Run pytest-cov  

현재 프로젝트의 구조는 아래와 같다.  
circleci 설정을 위한 circle.yml파일과 requirements.txt 파일 등의 설정 파일들이 존재하고
메인 코드인 _hansung-bab.py_, 테스트 코드인 _test_sample.py_ 가 존재한다.  

```
$ tree
.
[...]
├── circle.yml
├── hansung-bab.py
├── requirements.txt
└── test_sample.py
```  

<br/>  

### pytest-cov 실행 명령   

pytest-cov 의 실행 명령어는 `pytest --cov=[src] [test]` 의 형태이다. `[src]` 에는 소스코드가 위치한 폴더명 혹은 소스 파일명을 입력하고 `[test]`에는 테스트 코드가 위치한 폴더명 혹은 테스트 파일명을 입력한다. 즉, `[src]`에 coverage를 측정하고자 하는 소스를 입력하고 `[test]`에는 측정하기 위해 사용되는 테스트를 입력하는 것이다.  


```
$ pytest --cov=./ ./test_sample.py
```

으로 실행해보면 결과는 아래와 같다.  

![pytest cov result](/images/codecov/pytest-cov result.png)   

<br/>  

# CircleCI pytest-cov 연동하기  

git push 할때마다 CI가 code coverage를 측정해서 결과를 알려주도록 설정이 필요하다.  
circle.yml 파일을 아래와 같이 수정하여 빌드할 때 pytest-cov 명령을 수행하도록 하고,  
`codecov` 라는 명령 추가해서 code coverage 결과를 codecov.io에 업로드 하여 웹으로 확인할 수 있도록 한다.  

_circle.yml_  

```
test:
    override:
        - pytest --cov=./ ./test_sample.py
    post:
        - codecov
```  

<br/>  

# 결과 확인  

작성한 내용들을 push 한 후 [Circle CI Dashboard](https://circleci.com/dashboard) 에서 해당 빌드를 자세히 보면 pytest-cov와 codecov 명령을 수행하고 빌드가 성공한 것을 확인할 수 있다.  

![circleci pytest](/images/codecov/circleci pytest.png)    

![circleci codecov](/images/codecov/circleci codecov.png)   

[Codecov](https://codecov.io/gh) 에서는 code coverage 결과에 대한 자세한 내용을 확인할 수 있다.  

![result codecov](/images/codecov/result codecov.png)   

### github pull request codecov comment  

github repository 의 settings에 CircleCI 가 services로 등록이 되어있다면  

![github service](/images/codecov/github services.png)   

pull request 를 보낼때마다 codecov 가 coverage에 대한 정보를 커맨트로 달아준다.  

![codecov comment](/images/codecov/codecov comment.png)   

<br/>  

# 참고자료   

<https://github.com/codecov/example-python>  
