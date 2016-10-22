---
layout: post
title:  "django model created time test issue - datetime"
date:   2016-10-22
categories: test
comments: true
---

<br>  

> django에서 특정 model에 생설될 때의 시간을 기록하기 위한 created time 필드를 추가하고 단위테스트를 하는 과정에서 발생하는 문제에 대해서 다룬다.  

## Unittest  

데이터가 생성 될 때의 시간이 잘 저장되는지 테스트하기 위하여 Comment의 객체를 하나 생성하여 DB에 저장하고 직후의 시간 값을 저장해서 그 둘의 차이가 1초보다 작은지를 테스트한다.  

```python
from datetime import datetime, timedelta

def test_saving_create_time(self):
    comment_ = Comment()
    [...]
    comment_.save()
    time_after_create = datetime.now()

    self.assertGreater(timedelta(minutes=1), time_after_create - comment_.create_time)
```  

model에 시간을 저장하기 위한 필드를 추가한다. `auto_now_add=True`를 추가하면 데이터가 생성되어 처음 저장 될 때 동작한다. (`auto_now=True`는 데이터가 수정될 때마다 갱신된다.)  

```python
class Comment(models.Model):
    [...]
    create_time = models.DateTimeField(auto_now_add=True)
```

모델이 변경되었으니 `$ python manage.py makemigrations`을 실행시켜 준다.  
테스트를 돌려보면 아래와 같은 에러 메세지가 뜬다.  

```
TypeError: can't subtract offset-naive and offset-aware datetimes
```  

create_time의 dafault 값은 `django.utils.timezone.now` 으로 time zone이 `utc`로 지정된 것이고, 테스트 코드에서 datetime.now()는 time zone이 지정되지 않은 것으로 두 값의 type이 달라서 발생하는 에러이다.  

```python
def test_saving_create_time(self):
    comment_ = Comment()
    [...]
    comment_.save()
    time_after_create = datetime.now(timezone.utc)

    self.assertGreater(timedelta(minutes=1), time_after_create - comment_.create_time)
```  

테스트 코드의 현재 시간을 가져오는 부분에 timezone을 utc로 설정해주면 문제를 해결할 수 있다.  
