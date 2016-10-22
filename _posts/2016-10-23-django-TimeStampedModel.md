---
layout: post
title:  "django TimeStampedModel 상속 이용하기"
date:   2016-10-22
categories: test
comments: true
---

<br>  

django에서 models에 데이터 생성 시간, 업데이트 시간을 기록하기 위해 create_time, modified_time 필드를 설정해 주는 경우가 자주 있다. 이 필드들은 많은 모델들에서 사용되는 필드이므로 TimeStampedModel에 한 번만 필드를 지정해놓고 상속을 받아서 사용하는 것이 좋다. 일일이 두 가지 필드를 해당 모델들에 추가할 필요가 없고, 추가하지 않아서 발생하는 문제들을 방지할 수 있는 장점이 있다.  

### 추상화 기초 클래스  

_core/models.py_  
```python
from django.db import models

class TimeStampedModel(models.Model):
    created_time = models.DateTimeField(auto_now_add=True)
    modified_time = models.DateTimeField(auto_now=True)

    class Meta:
        abstract = True
```   

- _auto_now_add=True_  
데이터가 생성되어 처음 저장 될 때 시간이 기록된다는 의미이다.  
- _auto_now=True_  
데이터가 수정될 때마다 시간이 업데이트 된다는 의미이다.  
- _class Meta:  abstract = True_  
해당 클래스를 추사화 기초 클래스로 선언한다는 의미로 장고에서 마이그레이션을 실행할 때 timestampedmodel에 대한 테이블이 생성되지 않는다.  

<br>  

### 상속받은 클래스 사용 예시  

_board/models.py_  
```python
from django.db import models

from core.models import TimeStampedModel

class Comment(TimeStampedModel):
    [...]
```  

Comment 모델은 created_time, modified_time 필드를 따로 지정하지 않아도 TimeStampedModel을 상속 받았기 때문에 데이터가 생성될 때, 수정될 때의 시간이 기록된다.  
