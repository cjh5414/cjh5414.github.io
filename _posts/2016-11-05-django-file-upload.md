---
layout: post
title:  "django file upload 기능 구현하기 - Model Form 이용"
date:   2016-11-05
categories: django
---

<br>  

> djanog에서 file upload 기능을 구현하는데 modelform을 이용하였더니 편리하게 구현할 수 있었다. 그 과정에 대한 포스팅이다.  

<br>  

## MEDIA Setting  

upload file은 MEDIA_ROOT에 저장이 되고, 요청할 때는 MEDIA_URL을 이용한다.  

_settings.py_  

```python
MEDIA_URL = '/media/'
MEDIA_ROOT = os.path.join(BASE_DIR, 'media')
```  

개발 서버에서 uploaded file을 serve 할 수 있도록 아래의 설정이 필요하다.  

_urls.py_  

```
from django.conf import settings
from django.conf.urls.static import static

urlpatterns = [
    # ... the rest of your URLconf goes here ...
] + static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)
```  

<br>  

## Model   

title과 upload할 file에 대한 정보를 갖는 model.  
file은 존재하지 않는 경우도 있으므로 `null=True`로 설정해준다.  

_models.py_  

```python
from django.db import models


class UploadFileModel(models.Model):
    title = models.TextField(default='')
    file = models.FileField(null=True)
```  

<br>  

## ModelForm  

UploadFileModel에 대한 modelform을 생성한다.  

_forms.py_  

```python
from django import forms

from .models import UploadFileModel

class UploadFileForm(forms.ModelForm):
    class Meta:
        model = UploadFileModel
        fields = ('title', 'file')

    def __init__(self, *args, **kwargs):
        super(PostForm, self).__init__(*args, **kwargs)
        self.fields['file'].required = False
```  

__init__ 함수에서 `self.fields['file'].required = False` 설정을 통해 file 값이 없더라도 view에서 유효성 검사에서 오류를 발생시키지 않도록 해준다.  

<br>  


## HTML  

file upload를 하기 위해서는 form에 `enctype="multipart/form-data"`을 추가해줘야 한다.  
`novalidate`는 form 제출 시에 유효성 검사를 하지 않게 해준다.  


_upload.html_  

```
[...]
  <form method="post" enctype="multipart/form-data" novalidate>
    {% csrf_token %}
    {{ form.as_p }}
    <button type="submit">Upload</button>
  </form>
[...]
```  

upload된 파일은 file의 url을 href로 지정하고 download를 넣어주면 다운 받을 수 있다.  

_uploaded.html_  

```html
<a id="id_uploaded_file" href="{{ uploadFileModel.file.url }}" download>{{ post.file.name }}</a>
```  

<br>  

## Upload View  

UploadFileForm 객체를 생성하여 rendering 할 때 `form`으로 넘겨주면 html에서 `{{ form.as_p }}`을 통해 file 필드가 `<input type="file" name="" />`으로 생성된다.  
POST로 form이 제출되면 upload 파일의 정보는 request.FILES을 통해 전달되고 form 유효성 검사 후에 `form.save()`을 하면 settings에서 설정한 `MEDIA_ROOT` 위치에 파일이 저장된다.  

_views.py_  

```python
from django.http import HttpResponseRedirect
from django.shortcuts import render
from .forms import UploadFileForm

def upload_file(request):
    if request.method == 'POST':
        form = UploadFileForm(request.POST, request.FILES)
        if form.is_valid():
            form.save()
            return HttpResponseRedirect('/success/url/')
    else:
        form = UploadFileForm()
    return render(request, 'upload.html', {'form': form})
```  

<br>  

> 현재는 사용중인 Model에 file 필드만 추가하여서 구현하고 있지만 file을 다른 form으로 분류하여서 구현하는게 더 좋다고 한다. 이 점에 대해서는 더 자세히 공부한 후에 포스팅할 예정이다.  

<br>   

## 참고자료  

<https://docs.djangoproject.com/el/1.10/topics/http/file-uploads/>
<https://simpleisbetterthancomplex.com/tutorial/2016/08/01/how-to-upload-files-with-django.html>
