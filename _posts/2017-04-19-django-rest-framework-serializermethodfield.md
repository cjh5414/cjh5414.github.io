---
layout: post
title:  DRF(Django Rest Framework) SerializerMethodField 이용하여 특정 필드 값 변경해서 반환하기
tags:   [Django, DRF]
---

> DRF(Django Rest Framework)에서 ModelSerializer 을 이용하면 `class Meta:`의 설정값에 따라서 REST API를 만들어준다. 기본적으로 model의 필드 값들을 JSON형태로 반환해주는데 날짜, 시간 등과 같은 값들을 원하는 표현법으로 변경하여 반환하고 싶은 경우가 있다. 이 때, SerializerMethodField을 이용하여 해당하는 필드의 반환 형태를 지정해주는 함수를 정의하면 쉽게 변경할 수 있다. [DRF API Guide](http://www.django-rest-framework.org/api-guide/fields/#serializermethodfield) 에서는 User의 가입일자 필드 값을 가지고 가입한지 며칠이 지났는지의 값으로 바꿔서 반환하는 방법의 예시를 설명한다.  

<br/>  

### Example  

`Uer` 모델에 `date_joined`라는 가입일자의 필드 값이 존재한다. User API를 호출했을 때, `days_since_joined`라는 값으로 가입한지 며칠이 됐는지의 값을 반환해주는 방법에 대한 예시이다.  

```python
from django.contrib.auth.models import User
from django.utils.timezone import now
from rest_framework import serializers

class UserSerializer(serializers.ModelSerializer):
    days_since_joined = serializers.SerializerMethodField()

    class Meta:
        model = User

    def get_days_since_joined(self, obj):
        return (now() - obj.date_joined).days
```  

현재 날짜와 필드 값의 차이를 `일` 단위로 바꿔주는 `def get_days_since_joined(self, obj)`라는 함수를 구현한다. obj로는 model 객체를 넘겨받기 때문에 `obj.date_joined`와 같이 사용할 수 있다. API에 추가하고자 하는 필드 값을 `days_since_joined`로 추가해주고 `serializers.SerializerMethodField()`를 할당한다.

> `SerializerMethodField(method_name=None)` : 함수명을 `get_<field_name>`으로 지정하면 기본값으로 설정 돼 있기 때문에 `method_name`에 함수명을 지정하지 않아도 되지만 함수명을 `get_<field_name>` 형태로 하지 않는다면 `SerializerMethodField(method_name)`과 같이 SerializerMethodField의 인자로 함수명을 넘겨주는 방식으로 사용할 수 있다.  

<br/>  

### Error  

```
The field 'field_name' was declared on serializer StatusByDaySerializer, but has not been included in the 'fields' option.
```   

이런 에러가 발생한 경우에는

```python
class Meta:
    model = User
    fields = ( ... , 'field_name')
```  

`class Meta:` 에 fields 에 해당하는 필드의 이름을 추가해주면 된다.  

<br/>  

### 참고자료   

- <http://www.django-rest-framework.org/api-guide/fields/#serializermethodfield>  
