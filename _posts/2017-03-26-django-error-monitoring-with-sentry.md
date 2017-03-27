---
layout: post
title:  Sentry를 이용한 Django 에러 모니터링
tags:   [Django]
---

> Sentry를 이용하여 Django 에서 발생하는 에러를 모니터링하는 방법에 대해서 알아보았다. Django 프로젝트에 Sentry를 설정해주면 프로그램 구동 중에 발생하는 Exception과 Error를 web상에서 편리하게 확인할 수 있다.  

[Sentry](https://sentry.io/) 에서 들어가서 회원가입을 하면 새로운 프로젝트를 생성하는 페이지가 뜬다.  

![create project](/images/django-sentry/create project.png)  

프로젝트명을 입력하고 `Create Project` 버튼을 누르면 어플리케이션의 플랫폼 혹은 언어를 선택하는 화면으로 넘어간다.  

![configure app](/images/django-sentry/configure app.png)  

두번째에 보이는 `Django`를 클릭하면 [Sentry Django](https://docs.sentry.io/clients/python/integrations/django/) docs 에서 기본적인 설정과 사용법에 대해 요약해놓은 [Configure Django](https://sentry.io/linku-fa/test/getting-started/python-django/)라는 제목의 문서 페이지로 이동한다.  

![configure django](/images/django-sentry/configure django.png)  

### Installation  

pip를 통해 raven을 설치한다.  

```
$ pip install raven --upgrade
```   

### Setup  

django 프로젝트의 _settings.py_ 의 `INSTALLED_APPS`에 `raven`을 추가하고 `RAVEN_CONFIG` 을 아래의 형식에 맞게 설정해준다. 위의 과정을 잘 따라왔다면 문서에 해당 프로젝트의 `<key>`, `<project>` 값이 들어가있으므로 복사, 붙여넣기 하면 된다.   

_settings.py_  

```python
INSTALLED_APPS = (
    'raven.contrib.django.raven_compat',
)
```   

```python
import os
import raven

RAVEN_CONFIG = {
    'dsn': 'https://<key>:<secret>@sentry.io/<project>',
    # If you are using git, you can also automatically configure the
    # release based on the git info.
    'release': raven.fetch_git_sha(os.path.dirname(os.pardir)),
}
```  

설정이 잘 됐는지 확인하기 위해 아래의 명령을 실행해봤더니 에러가 발생했다.  

```
$ python manage.py raven test
...
raven.exceptions.InvalidGitRepository: Cannot identify HEAD for git repository at
```   

`'release': raven.fetch_git_sha()` 에는 .git 파일이 존재하는 위치와 동일한 path 가 들어가야한다.  
django에 설정된 `BASE_DIR`을 이용하여 입력해주면 된다. (프로젝트르 어떤 경로에서 git으로 관리하는지에 따라 달라질 수 있다.)  

```python
'release': raven.fetch_git_sha(BASE_DIR),

or

'release': raven.fetch_git_sha(os.path.dirname(BASE_DIR)),
```  

다시 돌려보면 제대로 동작하는 것을 확인할 수 있고  

```
$ python manage.py raven test
Client configuration:
  base_url       : https://sentry.io
  project        : 152001
  public_key     : ...
  secret_key     : ...

Sending a test message... Event ID was '71970aeea118475b8c71f06aa0813bb5'
```   

base_url 으로 설정되어있는 <https://sentry.io/> 에 들어가면 `This is a test message generated using raven test` 라는 이름의 Issue를 확인할 수 있다.  

![result](/images/django-sentry/result.png)  

![detail result](/images/django-sentry/detail result.png)  

### Exception  

Exception을 capture 하는 방법은 `client.captureException()`을 이용하면 된다. 관련 모듈을 import하고 원하는 exception 위치에 코드를 넣어주면 된다. 간단히 테스트해보고 싶다면 아래와 같이 _settings.py_ 맨 하단에 예외처리를 해주면 된다.  

_settings.py_  

```python
RAVEN_CONFIG = {
    # ...
}

from raven.contrib.django.raven_compat.models import client

try:
    2 / 0
except ZeroDivisionError:
    client.captureException()
```  

마찬가지로 <https://sentry.io/> 에서 `ZeroDivisionError` 라는 이름으로 발생한 예외에 대한 내용을 자세히 볼 수 있다.  

![detail result2](/images/django-sentry/detail result2.png)  
