---
layout: post
title:  Django REST Framework Token Authentication
tags:   [Python, Django, DRF]
---

> Django REST Framework에서 Token을 이용해서 인증을 하는 방법에 대해서 알아본다. [Django REST Framework TokenAuthentication](http://www.django-rest-framework.org/api-guide/authentication/#tokenauthentication) 을 보고 따라해보면 쉽게 적용할 수 있지만 `AbstractUser`을 상속 받아서 User model을 구현한 경우에는 `{'non_field_errors': ['Unable to log in with provided credentials.']}` 라는 에러가 발생했다. serializer에서 user의 password를 저장할 때 hashing을 하지 않기 때문에 패스워드가 불일치하여 발생하는 문제로 `create`메소드를 Overriding하여 `set_password`를 적용하면 해결된다.  

<br/>  

## Setup  

Django REST Framework Token Authentication을 사용하기 위해 필요한 앱과 패키지를 _settings.py_ 에 설정해준다.  

_settings.py_  

```python
INSTALLED_APPS = [
    # ...
    'rest_framework',
    'rest_framework.authtoken',
]

REST_FRAMEWORK = {
    'DEFAULT_AUTHENTICATION_CLASSES': (
        'rest_framework.authentication.BasicAuthentication',
        'rest_framework.authentication.SessionAuthentication',
    )
}
```  

<br/>  

## URL  

인증을 요청할 때 사용할 원하는 URL을 다음과 같이 지정한다.  

_urls.py_  

```python
from rest_framework.authtoken.views import obtain_auth_token

urlpatterns = [
    # ...
    url(r'^api-token-auth/', views.obtain_auth_token)  
    # url(r'^login/', views.obtain_auth_token)  
]
```  

<br/>  

## User Model  

`User` model 은 `AbstractUser`를 상속받아서 구현했고 email을 `username`으로 사용하고 있다.  
`User`가 생성될 때 `Token`을 자동으로 생성하기 위해 아래의 코드를 추가해준다.  

_models.py_  

```python
from django.conf import settings
from django.db.models.signals import post_save
from django.dispatch import receiver
from rest_framework.authtoken.models import Token

class User(AbstractUser):
    username = models.EmailField(unique=True, null=False, max_length=254)
    # ...


@receiver(post_save, sender=settings.AUTH_USER_MODEL)
def create_auth_token(sender, instance=None, created=False, **kwargs):
    if created:
        Token.objects.create(user=instance)
```

<br/>  

## Result & Error  

제대로 동작하는지 확인해보기 위해 `User`를 생성하고 CLI를 통해 인증 요청을 해본다.  

```
$ curl -X POST -d "username=test@email.com&password=password1234" http://localhost:8000/api-token-auth/
{"non_field_errors":["Unable to log in with provided credentials."]}
```   

인증 요청은 지정된 URL로 로그인 하고자 하는 `username`, `password` 의 값을 JSON 혹은 form으로 POST 요청을 하면된다. 기대한 값은 `{ 'token' : '9944b09199c62bcf9418ad846dd0e4bbdfc6ee4b' }` 이었지만 로그인 할 수 없다는 에러메세지가 반환된다. django의 admin 페이지를 통해 확인해봤더니 `createsuperuser`를 통해 생성된 User에 대해서는 `token`이 반환되지만 그렇지 않은 일반 User에 대해서는 에러가 발생했다. 그 이유는 상속받아서 구현한 User model을 Serializer 하는 과정에서 password의 hashing이 누락 돼서 발생한 것으로 판단된다. 이를 해결하기 위해 아래와 같이 `set_password` 을 사용하여 추가적인 코드를 작성해주면 해결된다.  

_serializer.py_  

```python
class UserSerializer(serializers.HyperlinkedModelSerializer):
    def create(self, validated_data):
        password = validated_data.pop('password', None)
        instance = self.Meta.model(**validated_data)
        if password is not None:
            instance.set_password(password)
        instance.save()
        return instance

    #...
```  

```
$ curl -X POST -d "username=test@email.com&password=password1234" http://localhost:8000/api-token-auth/
{ 'token' : '9944b09199c62bcf9418ad846dd0e4bbdfc6ee4b' }
```    
