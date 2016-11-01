---
layout: post
title:  "django Key Error request.POST.get() 사용 문제"
date:   2016-10-27
categories: django
comments: true
---

<br>  

> kboard 프로젝트에서 게시글 수정하는 기능을 구현하는 과정에서 발생한 문제이다. 결론부터 말하자면, 테스트 코드를 돌려보던 중에 Key Error 에러가 발생해서 views에 request.POST.get()을 이용하였더니 문제가 해결됐다. 하지만 Key Error가 발생한다고 해서 request.POST.get()을 이용하여 문제를 바로 해결하는 것은 좋지 않은 선택이다.  

<br>  

### 게시글 수정에 대한 views와 test code  

_views.py_  

```python
def edit_post(request, post_id):
    if request.method == 'POST':
        [...]
        post.title = request.POST['title']
        return redirect(post.board)
    [...]
```  

_unittest_  

```python
def test_POST_redirects_to_post_list(self):
    response = self.client.post( [...] ), {
        'edit': 'Edited title',
    })

    self.assertRedirects( [...] )
```  

<br>  

### Error  

테스트를 동작 시켰을 때 발생한 오류이다  

```
raise MultiValueDictKeyError(repr(key))
django.utils.datastructures.MultiValueDictKeyError: "'title'"
```  

<br>  

### 원인  

구글링을 해보니 keyerror가 발생할 경우 `request.POST['key']` 대신에 `request.POST.get('key', '')` 을 사용하면 된다고 나와있었다.   

views를 아래와 같이 변경하였더니 테스트를 통과했다.   

_views.py_  

```python
def edit_post(request, post_id):
    if request.method == 'POST':
        [...]
        post.title = request.POST.get('title', '')
        return redirect(post.board)
    [...]
```  

두 가지의 차이점이 궁금해서 더 찾아봤는데 `request.POST` 는 request의 POST 값들을 dictionary 형태로 반환해 준다. python 문법에 의해 반환된 dict은 `['key']`을 이용하면 'key'에 해당하는 값을 리턴해 주는데 존재하지 않으면 KeyError를 발생시킨다. dict에서 특정 key의 값을 얻는 다른 방법으로 `.get('key')` 을 이용하면 존재하지 않은 key일 경우 None을 반환하는 차이점이 있고, 추가로 `.get('key', 'defaultvalue')` 와 같이 key 값이 없을 경우 default 값을 설정해 줄 수 있다.   

위의 코드에서 error의 원인과 테스트를 통과할 수 있었던 이유를 살펴보면 views에서는 `title` 값을 원했는데 test코드에서 key 값을 `edit`로 잘못 지정해서 views에서 원하는 값이 없기 때문에 keyerror가 발생한 것이고,
.get()을 이용해서 `title` 값이 존재하지 않더라도 기본 값으로 ''이 설정됐기 때문에 테스트를 통과한 것이다.  

하지만 이 경우 테스트의 의도와는 맞지 않게 된다. `title` 의 값이 제대로 들어갔는지를 확인하는 테스트가 아니긴 하지만 `title` 에 내가 원하던 'Edited title'값이 아닌  ''값이 들어간 상태로 테스트가 통과한 것이다. 여기서는 이상이 없었지만 분명히 문제가 있는 코드이다.

<br>  

### 결론  

key Error가 발생한다고 해서 무조건 .get()을 사용하여 해결하기 보다는 원인을 분석하여 적절한 해결방법을 찾아보는 것이 좋다고 생각된다.

위의 경우에는 POST의 key에서 발생한 오타를 수정해주면 .get()을 사용하지 않더라고 테스트가 잘 통과한다. ( 결론은 오타 문제였다.. ) 조금 더 자세히 생각해보면 이 경우에는 .get을 사용하면 안되는 것이다. .get()을 사용하고 테스트가 아닌 실제 template 태그에서 key값이 잘못됐다고 가정하면, 사용자가 수정한 값은 무시되고 항상 views에서 .get()에서 지정한 default 값으로 반영이 될 것이다.  

<br>  

### 참고자료  

<https://www.quora.com/What-is-the-difference-between-request-get-and-request-GET-get-in-django>
