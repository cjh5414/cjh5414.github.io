---
layout: post
title:  hashed password로 Django User Model instance 생성하기
tags:   ['Django', 'Python']
---

> Django에서 제공하는 User를 상속 받아서 사용하고 있다. 테스트할 때 매번 User 객체를 생성하는 것이 번거로워서 `RunPython`을 이용하여 migrate 할 때 테스트를 위한 User model instance 를 생성했는데 password가 해싱되지 않고 저장되는 문제가 발생했다. `User.objects.create()` 할 때 password를 hashing하여 생성하는 방법에 대해서 알아본다.  

<br/>  

## User model  

```python
from django.contrib.auth.models import AbstractUser
from django.core.validators import RegexValidator


class User(AbstractUser):
    phone_regex = RegexValidator(regex='^\d{11}$', message='Phone length has to be 11 & Only number')

    username = models.EmailField(unique=True, null=False, max_length=254)
    phone_number = models.CharField(max_length=11, validators=[phone_regex])
    # ...
```  

User model은 `AbstractUser` 를 상속 받고 email을 username으로 사용하고 있다.  

<br/>  

## Create User instance  

```python
User.objects.create(
    username="test@email.com",
    password="pwd1234",
    phone_number="01012341234")
```  

위와 같이 password를 지정해서 User instance를 생성하면 password가 hashing 되지 않고 DB에 "pwd1234" 값 그대로 저장이 된다. password를 hashing하여 instance를 생성하는 방법을 검색해보면 `set_password()` 을 이용하는 방법들이 많이 나오는데,

```python
user = User(
    username="test@email.com",
    phone_number="01012341234")
user.set_password("pwd1234")
user.save()
```   

적용해보면 'set_password'를 찾을 수 없다는 에러가 발생한다.  

```
AttributeError: 'User' object has no attribute 'set_password'
```   

[Django Docs's Password management in Django ](https://docs.djangoproject.com/en/1.11/topics/auth/passwords/#module-django.contrib.auth.hashers) 을 참고해보면 `make_password()` 을 이용하면 hashed password를 생성할 수 있다고 한다.    

```python
from django.contrib.auth.hashers import make_password

hashed_password = make_password("pwd1234")

User.objects.create(
    username="test@email.com",
    password=hashed_password,
    phone_number="01012341234")
```  

<br/>  

## RunPython  

`RunPython`을 이용하여 User instance를 생성하면 migrate 단계에서 User 데이터를 생성하여 DB에 저장된다.  

```python
from __future__ import unicode_literals
from django.db import migrations, models
from django.contrib.auth.hashers import make_password


def forwards_func(apps, schema_editor):
    User = apps.get_model("myapp", "User")

    hashed_password = make_password("pwd1234")
    User.objects.create(username="testman@email.com", password=hashed_password, phone_number="01012341234")


def reverse_func(apps, schema_editor):
    Meeting = apps.get_model("myapp", "User")
    db_alias = schema_editor.connection.alias


class Migration(migrations.Migration):
    dependencies = [
        ('myapp', '0001_initial'),
    ]

    operations = [
        migrations.RunPython(forwards_func, reverse_func),
    ]
```
