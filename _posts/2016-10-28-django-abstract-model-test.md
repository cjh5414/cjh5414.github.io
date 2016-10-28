---
layout: post
title:  "django abstract model test"
date:   2016-10-28
categories: test
---

<br>  

여러가지 모델에 공통적으로 필요한 created_time 필드를 가지고 있는 TimeStampedModel 추상 모델을 상속 받아서 모델들을 구현하고 있다. (TimeStampedModel abstract model에 관한 내용은 [여기](https://cjh5414.github.io/django-TimeStampedModel/)를 참고하면 된다.)  
각각의 모델들에 created_time이 잘 저장되는지 확인하기 위해 테스트 코드를 작성하는데 새로운 모델을 추가할 때마다 테스트 코드를 추가하면 코드의 중복이 발생한다. 이를 해결하기 위해서는 추상 모델인 TimeStampedMode 에서 한 번만 테스트를 작성하면 된다. 하지만 abstract model을 테스트 하기 위해서는 DB에 테이블이 생성되지 않기 때문에 일반적인 model 테스트와는 다르게 추가적인 작업이 필요하다.  

<br>  

### TimeStampedModel  

_core/models.py_  

```python
class TimeStampedModel(models.Model):
    created_time = models.DateTimeField(auto_now_add=True)

    class Meta:
        abstract = True
```  

<br>  

### Abstract model test  

우선 abstract model에 대한 테스트가 정말 안되는지 확인해봤다.  

_core/tests/test_models.py_  

```python
from datetime import datetime
from datetime import timezone
from datetime import timedelta

from django.test import TestCase

from core.models import TimeStampedModel

class TestTimeStampedModel(TestCase):
    def test_created_time_after_create_TimeStampedModel(self):
        test_model = TimeStampedModel()
        test_model.save()

        time_after_create = datetime.now(timezone.utc)

        self.assertGreater(timedelta(minutes=1), time_after_create - test_model.created_time)
```   

creaed_time이 제대로 저장 됐는지 확인하기 위해 TimeStampedModel의 객체를 생성하고 저장한 후 시간을 기록한다. 기록한 시간과 created_time의 오차가 1초 이내 인지를 테스트하는 코드이다.  

테스트를 돌려보면 `AttributeError: 'NoneType' object has no attribute 'attname'`라는 에러가 발생한다. 에러 메세지의 정확한 의미는 잘 모르겠지만 예상했던대로 save() 부분에서 에러가 발생했다.  

<br>  

### TestModelInheritedTimeStampedModel  

실제로 모델을 사용하는 것과 같이 TimeStampedModel을 상속 받은 test model을 생성하여 테스트 하면 해결이 된다.  

test code에 TimeStampedModel을 상속 받은 TestModelInheritedTimeStampedModel 테스트 클래스를 생성하고 테스트에서 생성하는 객체의 모델을 TestModelInheritedTimeStampedModel으로 바꿔준다.

_core/tests/test_models.py_  

```python
[...]
class TestModelInheritedTimeStampedModel(TimeStampedModel):
    class Meta:
        app_label = 'core'


    class TestTimeStampedModel(TestCase):
        def test_created_time_after_create_TimeStampedModel(self):
            test_model = TestModelInheritedTimeStampedModel()
            test_model.save()

[...]
```  

<br>  

### app_label    

app_label = 'app_name'을 지정해주는 이유는 django는 기본적으로 model을 생성할 때 class가 존재하는 바로 위의 파일(여기서는 tests)을 앱으로 판단하여 거기에 모델을 생성한다. 하지만 test라는 앱은 존재하지 않아서 INSTALLED_APPS에서 찾을 수가 없기 때문에 아래와 같은 에러가 발생한다.  

```
RuntimeError: Model class tests.test_models.TestModelInheritedTimeStampedModel doesn't declare an explicit app_label and isn't in an application in INSTALLED_APPS.
```  

`class Meta:`안에 `app_label`을 앱 이름으로 지정해줘야 한다.(여기서는 core)  

<br>   

### 참고자료  

<https://www.kurup.org/blog/2014/07/21/django-test-models>  
