---
layout: post
title:  Django REST Framework OneToMany 관계 Serializer에 추가하기
tags:   [JPA, Java]
---

> Django REST Framework에서 model을 정의하고 Serializer, ViewSet, Router 만 간단히 구현하면 REST API를 편리하게 이용할 수 있다. `Meeting`에 대한 REST API가 구현돼 있고 `SubImage` 라는 model과 OneToMany 관계를 가지고 있다. 이 때, `Meeting`에 대한 API를 호출하면 연관된 `SubImage` 정보까지 같이 반환되도록 하는 Serializer를 추가하는 방법에 대해서 다룬다.  

<br/>  

## Example  

_models.py_   

```python
class Meeting(models.Model):
    title = models.CharField(max_length=30)
    place = models.CharField(max_length=30)
    # ...

class SubImage(models.Model):
    path = models.ImageField(default=SAVED_MEETING_DEFAULT_IMAGE_NAME)
    meeting = models.ForeignKey('Meeting', related_name='sub_images', on_delete=models.CASCADE, blank=True)
```  

`Meeting` model에 대한 REST API가 구현이 돼 있고 `SubImage` model에서 `Meeting`을 외래키로 참조하는 OneToMany 관계를 맺고있다.  

<br/>  

## Serializer  

```python
class MeetingSerializer(serializers.HyperlinkedModelSerializer):
    class Meta:
        model = Meeting
        fields = ('title', 'place', ...)
```  

`MeetingSerializer`가 구현돼 있을 때, `Meeting` API를 호출했을 때 `SubImage`에 대한 정보도 같이 출력되도록 하려면 아래와 같이 `SubImageSerializer`를 구현하고 추가해주면 된다.  

```python
class SubImageSerializer(serializers.HyperlinkedModelSerializer):
    class Meta:
        model = SubImage
        fields = ('path', 'meeting')


class MeetingSerializer(serializers.HyperlinkedModelSerializer):
    sub_images = SubImageSerializer(many=True, read_only=True)

    class Meta:
        model = Meeting
        fields = ('title', 'place', 'sub_images', ...)
```  


## Result  

http://localhost:8000/meetings/1/ GET Request

```json
{
    "title": "test title",
    "place": "test place",
    "sub_images": [
        {
            "path": "http://localhost:8000/media/favicon_JOkdl66.png",
            "meeting": "http://localhost:8000/meetings/1/"
        },
        {
            "path": "http://localhost:8000/media/favicon_DWdw3cH.png",
            "meeting": "http://localhost:8000/meetings/1/"
        }
    ]
}
```  
