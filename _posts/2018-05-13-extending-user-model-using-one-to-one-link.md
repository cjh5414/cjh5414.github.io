---
layout: post
title:  One-To-One Relationships(일대일 관계)를 이용한 Django User Model 확장하기
tags:   ['Django']
---

> Django의 User Model에 One-To-One 관계를 이용하여 새로운 Model을 추가하는 방법을 알아보던 중에 Django의 User Model을 확장해서 사용하는 방법에 대한 좋은 글을 발견해서 번역하고 더 이해하기 쉽도록 추가적인 설명을 덧붙였습니다. 크게 4가지 방법이 있고 목적과 용도가 조금씩 다릅니다. 이 글은 그 중에 하나인 One-To-One 관계를 이용하여 Django의 User Model 을 확장하는 방법에 대한 내용입니다.  
원본 : [How to Extend Django User Model](https://simpleisbetterthancomplex.com/tutorial/2016/07/22/how-to-extend-django-user-model.html)    

<br/>  

## 언제 One-To-One 관계를 사용할까?  

One-To-One 관계는 이미 존재하는 어떤 Model에 대해 추가적인 정보를 저장할 필요가 있지만 그 Model의 핵심 역할과 크게 관련이 없는 경우 새로운 모델을 추가하고 One-To-One 관계로 연결하여 사용합니다.  

Django의 User Model의 경우, 인증 절차(the authentication process)와 관련이 없는 생일, 자기소개, 주소 등의 Profile 정보를 추가할 때 주로 사용됩니다.  

One-To-One 관계로 연결된 모델은 User Model 과는 별개로 자체 데이터베이스 테이블을 가지게 됩니다.

<br/>  

## One-To-One 관계를 이용하여 Django User Model 확장하기  

앞으로 소개하는 내용은 개인적으로 제가 주로 사용하는 방법으로 여러분들 또한 찾고 있던 방법일 가능성이 높습니다. User Model과 관련있는 추가적인 정보들을 저장하기 위해 새로운 Model을 만들 것입니다.  

저는 보통 이 Model 에 대해 `Profile` 이라는 이름을 사용합니다.   

```python
from django.db import models
from django.contrib.auth.models import User

class Profile(models.Model):
    user = models.OneToOneField(User, on_delete=models.CASCADE)
    bio = models.TextField(max_length=500, blank=True)
    location = models.CharField(max_length=30, blank=True)
    birth_date = models.DateField(null=True, blank=True)
```  

이제 **signals** 을 정의하여 User 객체가 생성되고 수정될 때 관련된 `Profile` Model 또한 자동으로 생성되고 업데이트 되도록 할 것입니다.  

```python
from django.db import models
from django.contrib.auth.models import User
from django.db.models.signals import post_save
from django.dispatch import receiver

class Profile(models.Model):
    user = models.OneToOneField(User, on_delete=models.CASCADE)
    bio = models.TextField(max_length=500, blank=True)
    location = models.CharField(max_length=30, blank=True)
    birth_date = models.DateField(null=True, blank=True)

@receiver(post_save, sender=User)
def create_user_profile(sender, instance, created, **kwargs):
    if created:
        Profile.objects.create(user=instance)

@receiver(post_save, sender=User)
def save_user_profile(sender, instance, **kwargs):
    instance.profile.save()
```  

**post_save** signal 이라는 것을 사용하는 것인데, `sender=User` 설정에 의해서 User의 **save()** 가 호출될 때마다 그 직후에 `create_user_profile`, `save_user_profile` 이 호출됩니다.  

Django Template과 User의 View method 예제를 살펴봅시다.    

```html
<h2>{{ user.get_full_name }}</h2>
<ul>
  <li>Username: {{ user.username }}</li>
  <li>Location: {{ user.profile.location }}</li>
  <li>Birth Date: {{ user.profile.birth_date }}</li>
</ul>
```   

```python
def update_profile(request, user_id):
    user = User.objects.get(pk=user_id)
    user.profile.bio = 'Hello i am jihun ...'
    user.save()
```   

`Profile` 모델의 값을 변경하고 있는데 관련된 user 객체를 사용하여 쉽게 변경할 수 있고 앞에서 구현한 `signal`로 인해 `Profile`의 `save()` 함수를 호출하지 않아도 User 객체를 저장함으로써 Profile 값을 저장할 수 있습니다. `Profile`의 `save()`를 호출할 필요가 없는 것입니다.  
즉, `Profile`을 모델을 추가로 구현하였지만 마치 User에 속해있는 것처럼 하나로 편리하게 관리할 수 있습니다.  

<br/>  

Django Forms을 사용하고 있는 경우라면 어떨까요?  

한 번에 두 개 이상의 form을 사용할 수 있다는 것을 알고계셨나요?
아래의 예제를 살펴봅시다.   

_forms.py_   

```python
class UserForm(forms.ModelForm):
    class Meta:
        model = User
        fields = ('first_name', 'last_name', 'email')

class ProfileForm(forms.ModelForm):
    class Meta:
        model = Profile
        fields = ('url', 'location', 'company')
```   

<br/>  

_views.py_  

```python
@login_required
@transaction.atomic
def update_profile(request):
    if request.method == 'POST':
        user_form = UserForm(request.POST, instance=request.user)
        profile_form = ProfileForm(request.POST, instance=request.user.profile)
        if user_form.is_valid() and profile_form.is_valid():
            user_form.save()
            profile_form.save()
            messages.success(request, _('Your profile was successfully updated!'))
            return redirect('settings:profile')
        else:
            messages.error(request, _('Please correct the error below.'))
    else:
        user_form = UserForm(instance=request.user)
        profile_form = ProfileForm(instance=request.user.profile)
    return render(request, 'profiles/profile.html', {
        'user_form': user_form,
        'profile_form': profile_form
    })
```   

<br/>  

_profile.html_  

```html
<form method="post">
  {% csrf_token %}
  {{ user_form.as_p }}
  {{ profile_form.as_p }}
  <button type="submit">Save changes</button>
</form>
```   
