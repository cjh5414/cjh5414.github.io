---
layout: post
title:  "django model form is_valid() saves instance automatically"
date:   2016-11-09
categories: django
---

<br>  

> modelform의 form.is_valid() 메서드는 내부적으로 여러가지 일을 하는데 그 중에 하나는 Model Form의 데이터를 폼 인스턴스로 알아서 저장하는 것이다. 이 때 is_valid() 가 True인지 False인지 와는 관계가 없다.  

<br>  

_views.py_  

```python
def edit_post(request, post_id):
    post = Post.objects.get(id=post_id)

    if request.method == 'POST':
        post_form = PostForm(request.POST, instance=post)
        print(post.title) # 1.
        if post_form.is_valid():
            print(post.title) # 2.
            [...]
        else
            print(post.title) # 3.
            [...]
```  

Post를 수정하는 view이다. `2.`와 `3.`에서 기존의 post의 title 값을 기대하고 작업을 했는데 실제로는 reqeust.POST['title']의 값이 출력된다. `1.` 에서는 수정하기 전의 title 값을 출력한다. 그 이유는 위에서 언급했듯이 is_valid() 메서드에서 post 인스턴스의 값을 request.POST에 들어있는 값으로 자동으로 설정하기 때문이다.  

<br>  

```python
>>> Post.objects.create(title='origin title', content='content')
>>> post = Post.objects.get(id=1)
>>> form = PostForm({'title': 'edited title'}, instance=post)
>>> post.title
u'origin title'
>>> form.is_valid()
True
>>> post.title
u'edited title'
>>> post2 = Post.objects.get(id=1)  # DB로 부터 새로운 instance를 가져온다
>>> post2.title                     # DB의 title 값이 그대로이다.
u'origin title'
>>> form.save()                     # DB에 form데이터를 저장한다
>>> post3 = Post.objects.get(id=1)  # 다시 DB로 부터 새로운 instance를 가져온다
>>> post3.title                     # DB의 title 값이 변경되어있다.
u'edited title'
```  

python shell로 간단히 테스트해 본 결과이다.  

> is_valid() 를 호출하면 post instance에 form의 값들이 저장되지만 DB에 저장되는 것은 아니다. (post2으로 확인) save()함수를 호출하면 DB에 저장된다. (post3으로 확인)  

is_valid() 에서 유효성 검사의 성공 여부와 관계없이 form 데이터가 항상 instance에 저장되는 것을 상기하면서 활용하면 좋을 것 같다. 위의 예시 코드로 예를 들어보면, 유효성 검사에 실패했을 경우 사용자가 입력한 form의 데이터(post)와 실제 DB의 모델 instance(post3) 의 데이터를 따로 비교, 저장 할 수 있다.   
