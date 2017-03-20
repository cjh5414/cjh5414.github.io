---
layout: post
title:  Django REST Framework ImageField 이용하기
tags:   [Django]
---

> Django REST Framework 에서 특정 model에 ImageField를 추가하여 api를 통해 image file을 업로드, 요청 하는 방법에 대해서 알아본다. [Django REST Framework 시작하기](https://cjh5414.github.io/django-rest-framework/) 에서 사용했던 `Person` model과 `HyperlinkedModelSerializer`를 사용한다.  

<br/>  

## Person Model  

_myapp/models.py_  

```python
from django.db import models


class Person(models.Model):
    first_name = models.CharField(max_length=30)
    last_name = models.CharField(max_length=30)
    image = models.ImageField(default='media/default_image.jpeg')
```  

`Person` model에 `image` 라는 ImageField 를 정의한다. default 값이 필요하므로 기본 이미지로 하고싶은 이름을 지정한다.  

<br/>  

## PersonSerializer  

[DRF ImageField](http://www.django-rest-framework.org/api-guide/fields/#imagefield)을 참고하여 `HyperlinkedModelSerializer`을 이용하여 구현한 `PersonSerializer`에 image 필드를 추가한다.  

_myapp/serializers.py_  

```python
from myapp.models import Person
from rest_framework import serializers


class PersonSerializer(serializers.HyperlinkedModelSerializer):
    image = serializers.ImageField(use_url=True)

    class Meta:
        model = Person
        fields = ('first_name', 'last_name', 'image')
```  

`serializers` 의 ImageField를 이용하고, `class Meta`의 `fields`에 'image'를 추가한다.  

<br/>  

## MEDIA Settings  

image 파일들을 관리하기 위해 _tutorial/_ 위치에 _media_ 폴더를 생성하고, django에 `MEDIA_ROOT`, `MEDIA_URL`, _urls.py_ 를 설정해준다. _media_ 폴더에 `default` 이미지로 지정했던 `default_image.jpeg` 이미지를 넣어준다.    

_tutorial/settings.py_  

```python

# ...

MEDIA_URL = '/media/'
MEDIA_ROOT = os.path.join(BASE_DIR, 'media')
```  

_tutorial/urls.py_  

```python
from django.conf import settings
from django.conf.urls.static import static

urlpatterns = [
    # ...
] + static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)
```  

<br/>  

## Run  

```
$ python manage.py makemigrations
$ python manage.py migrate
$ python manage.py runserver
```  

browser를 키고 `localhost:8000` 에 접속하면 아래와 같은 화면이 뜬다.  

![home result](/images/DRF-imagefield/home-result.png)  

`http://localhost:8000/persons/` 링크를 클릭한 후에, 양식에 맞게 이름 데이터를 입력하고 테스트 image 파일을 선택한다. (여기서는 `test_image.png`라는 이름의 이미지 파일을 사용한다.)  

![input form](/images/DRF-imagefield/input-form.png)  

`POST` 버튼을 누르면 입력한 데이터의 POST 요청에 대한 정보가 출력된다. `image` 필드를 보면 `http://localhost:8000/media/test_image.png` 라는 url이 지정된 것을 확인할 수 있다.  

![request result](/images/DRF-imagefield/request-result.png)  

url을 클릭해보면 `test_image.png` 이미지가 보여진다.  

![test image result](/images/DRF-imagefield/test-image-result.png)  

_tutorial/media_ 폴더에 `default_image.png` 이미지도 존재하므로 아래와 같이 url을 요청하면 해당 이미지에 대한 응답이 온다.  

![default image result](/images/DRF-imagefield/default-image-result.png)  
