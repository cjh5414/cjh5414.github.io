---
layout: post
title:  Django User model password hashing 하기
tags:   ['Django']
---

> Django의 Auth User model을 사용하면 password를 hashing 해서 관리한다. 하지만 User를 생성하거나 Password를 변경할 때 password 가 hashing 되지 않고 원래의 값이 저장 되는데, 이 때문에 문제가 발생하는 경우가 있다.  

### 해결 방법   

```python
from django.contrib.auth.models import AbstractUser
from django.db.models.signals import pre_save
from django.dispatch import receiver
from django.contrib.auth.hashers import make_password, is_password_usable


class User(AbstractUser):
    name = models.CharField(max_length=30)
    phone_number = models.CharField(max_length=13)
    #...


@receiver(pre_save, sender=User)
def password_hashing(instance, **kwargs):
    if not is_password_usable(instance.password):
        instance.password = make_password(instance.password)
```  

django의 `@receiver`와 `signal` 을 이용하면 User model의 `save()`가 호출 되기 전에 어떤 작업을 추가할 수 있다.  

`is_password_usable()` 함수를 이용하여 현재 객체의 password 가 hashing된 상태인지 아닌지를 체크하고, hashing 되지 않았다면 `make_password()`를 이용하여 hashing하여 저장한다.  

이를 통해서 DB에 저장되는 User의 password 값은 항상 hashing 된 상태로 유지할 수 있다.  
