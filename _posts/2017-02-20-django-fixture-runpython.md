---
layout: post
title:  Django initial data | fixture 또는 RunPython 이용하기
tags:   [Python, Test, Django]
---

> Django에서 functional test를 작성하다 보면 DB에 특정 model의 instance값(initial data)을 test를 수행하기 전에 임의로 넣어줘야 하는 필요성이 있다. 예를 들어 Person(name, email)라는 model에 대해서 view가 DB에 있는 Person의 데이터를 잘 꺼내서 반환하는지를 테스트할 때, (jihun, jihun@gmail.com)와 같은 임의의 값으로 assert문을 작성하고 테스트를 실행하기 전에 그  테스트 값을 실제로 DB에 넣은 뒤에 실행해야 한다. 이렇게 테스트를 위해 사전에 DB에 값을 넣어주는 방법으로는 아래와 같은 방법들이 있다.  
- fixtures를 이용 - json으로 테스트 데이터를 저장해서 migration 전에 load한다.  
- Django RunPython을 이용 - migration 단계에서 테스트 데이터를 생성한다.  

<br/>  

## Example Code  

Meeting이라는 model이 존재한다.  

_[app_name]/models.py_   

```python
from django.db import models


class Meeting(models.Model):
    maker = models.TextField()
    name = models.TextField()
```  

view에서는 homepage('/')을 호출하면 DB에서 Meeting model에 대한 데이터를 모두 가져와서 name만 반환해준다.  

_[app_name]/views.py_   

```python
from django.http import HttpResponse
from moim.models import Meeting


def homepage(request):
    content = ''
    for obj in Meeting.objects.all():
        content += obj.name + "\n"
    return HttpResponse(content)
```  

DB에 'test meeting name'에 대한 테스트 데이터가 있다고 가정하고, homepage('/')를 요청하면 response로 모임의 이름인 'test meeting name'을 반환하는지를 확인하는 테스트 코드이다.

_homepage_test.py_  

```python
[...]
from selenium import webdriver
import pytest

def test_homepage():
    [...]

    driver = webdriver.Firefox()
    driver.get("http://localhost:8000")
    assert 'test meeting name' in driver.page_source
```   

<br/>  

## Fixtures를 이용하는 방법  

테스트 데이터를 아래와 같이 json파일로 저장한다. 파일의 위치는 app에 fixtures라는 폴더를 만들어서 저장한다.  

_[app_name]/fixtures/meeting-data.json_  

```json
[
  {
    "model": "moim.meeting",
    "pk": 1,
    "fields": {
      "maker": "test maker",
      "name": "test meeting name"
    }
  }
]
```  

json 파일을 직접 작성하지 않고 현재 DB에 있는 데이터를 json으로 뽑아내고 싶다면 아래의 명령을 사용하면 된다.  

```
$ ./manage.py dumpdata [app_name].[model_name] --indent [INDENT] > [fixture_name].json
```  

실제로 사용한 예시는 아래와 같다.  

```
$ ./manage.py dumpdata moim.meeting --indent 2 > meeting-data.json
```  

_meeting-data.json_ 파일이 생성되면 _[app_name]/fixtures_ 위치로 옮긴다.  

<br/>  

### Run Server

이제 migrate 후, 서버를 돌리기 전에 `loaddata` 명령을 사용해서 initial data를 load 할 수 있다.  

```
$ ./manage.py migrate
$ ./manage.py loaddata moim/fixtures/meeting-data.json
$ ./manage.py runserver
```   

DB를 열어보면 _meeting-data.json_ 의 값인 (maker="test maker", name="test meeting name")가 들어가 있는 것을 확인할 수 있다.  

```
$ pytest functional_tests/homepage_test.py
```  

테스트를 수행해보면 'test meeting name' 값이 있는 것을 확인하여 통과한다.   

<br/>  

## RunPython을 이용하는 방법  

RunPython을 이용하면 json 파일을 따로 저장해서 불러오지 않아도 된다. migration에 initial data를 추가하면 migrate 하는 과정에서 DB에 테스트 데이터를 넣을 수 있다.  

_0003_meeting_initialdata.py_  

```python
# -*- coding: utf-8 -*- 
from __future__ import unicode_literals
from django.db import migrations, models


def forwards_func(apps, schema_editor):
    Meeting = apps.get_model("moim", "Meeting")
    db_alias = schema_editor.connection.alias
    Meeting.objects.using(db_alias).bulk_create([
        Meeting(maker="test maker", name="test meeting name")
    ])


def reverse_func(apps, schema_editor):
    Meeting = apps.get_model("moim", "Meeting")
    db_alias = schema_editor.connection.alias
    Meeting.objects.using(db_alias).filter(maker="test maker", name="test meeting name").delete()


class Migration(migrations.Migration):

    dependencies = [
        ('moim', '0002_auto_20170219_0812'),
    ]

    operations = [
        migrations.RunPython(forwards_func, reverse_func),
    ]
```  

위와 같이 migrations을 추가하면,  

```
$ ./manage.py migrate
$ ./manage.py runserver
```   

migrate 명령을 수행할 때 fixture을 사용한 것과 마찬가지로 (maker="test maker", name="test meeting name") 이라는 데이터가 DB에 추가된다.  
_fixtures/meeting-data.json_ 을 사용하지 않으므로 제거하면 되고, load할 필요도 없다.   
