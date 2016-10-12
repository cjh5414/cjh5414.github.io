---
title: "Django에서 pagination 적용하기"
layout: post
date:   2016-10-11
categories: blog
comments: true
---

<br>  

## Example  

pagination을 적용하기 위한 예제이다.

![example](/images/pagination/example.png)  

<br>  

### pagination project & list app 생성  

```
$ django-admin startproject pagination
$ python manage.py startapp list
```  

### Model

_list/models.py_  

```python
from django.db import models

class Contacts(models.Model):
    text = models.TextField(default='')
```  

- _pagination/settings.py_ 의 INSTALLED_APPS에 'list' 추가  

- `$ python manage.py makemigrations` 실행  

- `$ python manage.py migrate` 실행  

- database에 'hello!', 'world' , 'a', ..., 'z' 추가  


### View

_list/views.py_  

```python
from django.shortcuts import render

from list.models import Contacts

def listing(request):

    contacts = Contacts.objects.all()
    return render(request, 'list.html', {'contacts': contacts})
```   

### templates

_list/templates/list.html_  

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>list</title>
</head>
<body>
    <h2>list</h2>
    {% for contact in contacts %}
    	{{ contact.text }}<br />
	{% endfor %}
</body>
```    

<br>  

## Django pagination  

<https://docs.djangoproject.com/en/1.10/topics/pagination/>  

Django Documentation 의 pagination 을 참고하여 적용해봤다.  

_list/views.py_  

```python
from django.core.paginator import Paginator, EmptyPage, PageNotAnInteger
from django.shortcuts import render

from list.models import Contacts

def listing(request):

    contact_list = Contacts.objects.all()

    paginator = Paginator(contact_list, 20) # Show 20 contacts per page

    page = request.GET.get('page')
    try:
        contacts = paginator.page(page)
    except PageNotAnInteger:
        # If page is not an integer, deliver first page.
        contacts = paginator.page(1)
    except EmptyPage:
        # If page is out of range (e.g. 9999), deliver last page of results.
        contacts = paginator.page(paginator.num_pages)

    return render(request, 'list.html', {'contacts': contacts})
```  

_list/templates/list.html_  

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>list</title>
</head>
<body>
    <h2>list</h2>
    {% for contact in contacts %}
    	{{ contact.text }}<br />
	{% endfor %}

    <span class="step-links">
        {% if contacts.has_previous %}
            <a href="?page={{ contacts.previous_page_number }}">previous</a>
        {% endif %}

        <span class="current">
            Page {{ contacts.number }} of {{ contacts.paginator.num_pages }}.
        </span>

        {% if contacts.has_next %}
            <a href="?page={{ contacts.next_page_number }}">next</a>
        {% endif %}
    </span>

</div>
</body>
</html>
```  

![page2](/images/pagination/page2.png)  

![page1](/images/pagination/page1.png)   

이와 같은 장고 pagination 튜토리얼에서 보여주는 스타일 말고도 장고에서 제공하는 `Paginator`를 잘 활용하면 자신이 원하는 pagination 스타일을 구현할 수 있다.  

하지만 누군가 이미 해놨을테니 검색을 해봤다.

- django EL(Endless) pagination
- django-bootstrap-pagination
- django-pure-pagination

등 많은 패키지들이 존재했다.  
그 중에 django EL pagination을 설치해서 적용해봤다.  

<br>  

## django EL(Endless) pagination  


### Install & Settings

설치.  

```
$ pip install django-el-pagination
```  

_setting.py_ 파일에 아래의 내용을 추가한다.  

_pagination/settings.py_  

```
INSTALLED_APPS = [
    ...
    'list',
    'el_pagination',
]
```   

문서에 보면

```python
from django.conf.global_settings import TEMPLATES

TEMPLATES[0]['OPTIONS']['context_processors'].insert(0, 'django.core.context_processors.request')
```   

이 내용도 추가하라고 나와있지만 djaogn 1.10 버전에서는 'django.template.context_processors.request' 으로 바뀌어서
이미 추가되어 있기 때문에 할 필요 없다.

```python
TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [],
        'APP_DIRS': True,
        'OPTIONS': {
            'context_processors': [
                'django.template.context_processors.debug',
                'django.template.context_processors.request',
                'django.contrib.auth.context_processors.auth',
                'django.contrib.messages.context_processors.messages',
            ],
        },
    },
]
```  

<br>  

### Pagination Style  

Django EL Pagination에는 Digg-style, Twitter-style 두 가지 스타일이 존재한다. 이 외에도 추가적인 기능들도 있다. 자세한 내용은 아래 문서를 참고하면 된다.  

- [Digg-style](http://django-el-pagination.readthedocs.io/en/latest/digg_pagination.html)  

- [Twitter-style](http://django-el-pagination.readthedocs.io/en/latest/twitter_pagination.html)  

<br>  

### Digg-style  

![digg1](/images/pagination/digg1.png)  

_list/views.py_  

```python
from django.core.paginator import Paginator, EmptyPage, PageNotAnInteger
from django.shortcuts import render

from list.models import Contacts

def listing(request):

    contacts = Contacts.objects.all()

    return render(request, 'list.html', {'contacts': contacts})
```  


_list/template/list.html_  

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>list</title>
</head>
<body>
    <h2>list</h2>

    { % load el_pagination_tags % }

	  { % paginate contacts % }
	  { % for contact in contacts % }
      	{{ contact.text }} <br/>
	  { % endfor % }
	  { % show_pages % }

</div>
</body>
</html>
```  

문서를 참고해서 계속 따라해보면 몇 번째 항목들을 표시하고 있는지도 보여줄 수 있다.

![digg2](/images/pagination/digg2.png)  


<br>  

### Twitter-style  

![twitter](/images/pagination/twitter.png)  

[여기](http://django-el-pagination.readthedocs.io/en/latest/twitter_pagination.html) 를 참고해서 따라해보면 된다.

하지만 현재 django 버전과 맞지 않아서 몇 가지 문제가 발생한다.

_list/views.py_  

```python
return render_to_response(
    template, context, context_instance=RequestContext(request))
```

에서 문제가 발생한다.  
아래 코드로 바꿔주면 된다.  

```python
return render(request, template, context)
```  

_list/template/list.html_ 을 작성할 때  

```
"{ { block.super } } in a base template?" % self.__class__.__name__  
```  

라는 오류가 발생하면 html 파일에서 `{ { block.super } }`를 제거 해주면 된다.  

> twitter style 에서는 scroll을 해서 pagination을 하는 방법도 제공한다. 자세한 내용은 [여기](http://django-el-pagination.readthedocs.io/en/latest/twitter_pagination.html)를 참고하면 된다.  


## 참고자료  

<https://docs.djangoproject.com/en/1.10/topics/pagination/>  

<https://github.com/shtalinberg/django-el-pagination>  

<http://django-el-pagination.readthedocs.io/en/latest/>  
