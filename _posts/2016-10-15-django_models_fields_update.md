---
layout: post
title:  "django models fields update & test code 작성 문제"
date:   2016-10-15
categories: django
---

<br>  

django를 이용한 프로젝트 진행 중에 게시글 삭제 기능을 구현하고 단위 테스트를 하는 과정에서 문제가 발생했다.   

Post 라는 model에 is_delete 필드를 추가하여 삭제 여부를 판단하려고 한다.  

단위테스트는 선택한 post에 대해서만 is_delete의 값이 True로 바뀌는지를 확인한다.   
delete_post, other_post 객체를 생성하고 delete_post에 대해 삭제 처리를 한 후 is_delete 값들을 확인하는 것이다.

처음에 view의 delete_post함수와 테스트 코드를 아래와 같이 작성했다.  

_unittest_  

```python
def test_delete_only_post_selected_to_delete(self):
    delete_post = Post.objects.create(..)
    other_post = Post.objects.create(..)

    self.assertEqual(delete_post.is_delete, False)
    self.assertEqual(other_post.is_delete, False)

    [...] # delete_post 삭제 처리를 위한 view의 delete_post함수 호출

    self.assertEqual(delete_post.is_delete, True)
    self.assertEqual(other_post.is_delete, False)
```

_삭제 처리를 하는 views.py delete_post()함수의 일부_  

```python
post = Post.objects.get(id=post_id)
post.is_delete = True
```   

테스트를 돌려보니 아래와 같은 에러 메세지가 뜬다.  

```
Traceback (most recent call last):
[...] in test_delete_only_post_selected_to_delete
    self.assertEqual(delete_post.is_delete, True)
AssertionError: False != True
```  

post.is_delete 에 True 값이 제대로 들어가지 않았다.  
is_delete 값을 수정한 후 DB에 저장을 하지 않았기 때문에 제대로 동작하지 않는 것이다. post.is_delete = True 후에 post.save()를 추가해 줘야한다.  
[models fields update와 관련된 django docs](https://docs.djangoproject.com/en/1.10/ref/models/instances/)를 찾아보니 update 하고자 하는 fields를 명시 해주면, models의 모든 fields를 database에 update하는 것을 막아주기 때문에 약간의 성능 향상이 있다고 한다. 따라서 코드를 아래와 같이 변경해준다.  

```python
post = Post.objects.get(id=post_id)
post.is_delete = True
post.save(update_fields=['is_delete'])
```  

다시 테스트를 돌려보면 똑같은 에러 메세지가 그대로 뜬다.  

에러가 발생한 부분은 self.assertEqual(delete_post.is_delete, True) 여기이다.
몇 가지 테스트를 해봤더니 `delete_post = Post.objects.get(id=delete_post.id)` 를 추가해주면 테스트가 통과한다.  

```python
def test_delete_only_post_selected_to_delete(self):
    delete_post = Post.objects.create(board=self.default_board, title='delete post', content='content')
    other_post = Post.objects.create(board=self.default_board, title='other post', content='content')

    self.assertEqual(delete_post.is_delete, False)
    self.assertEqual(other_post.is_delete, False)

    [...] # delete_post 삭제 처리를 위한 view의 delete_post함수 호출

    delete_post = Post.objects.get(id=delete_post.id)
    other_post = Post.objects.get(id=other_post.id)

    self.assertEqual(delete_post.is_delete, True)
    self.assertEqual(other_post.is_delete, False)
```   

처음에 Post객체를 생성할 때 delete_post 레퍼런스가 같은 객체를 계속 가리키고 있기 때문에 is_delete 값이 변경 되면 delete_post 레퍼런스를 그대로 사용하여 테스트를 하면 되겠다고 생각했다. 하지만 view 함수를 통해 삭제를 처리하는 작업은 DB의 is_delete의 값을 True로 바꿔주는 것이기 때문에 메모리상에 존재하는 delete_post 레퍼런스 가리키고 있는 객체의 is_delete와는 관련이 없었다. 그렇기 때문에 DB의 값을 다시 가져와서 테스트를 하면 통과하는 것이다.  

메모리 상의 데이터와 DB의 데이터를 동기화 해주면 되는 것인데, refresh_from_db()를 사용하여 바꿔준다면 더 좋은 코드가 될 것이다.  

```python
def test_delete_only_post_selected_to_delete(self):
    delete_post = Post.objects.create(board=self.default_board, title='delete post', content='content')
    other_post = Post.objects.create(board=self.default_board, title='other post', content='content')

    self.assertEqual(delete_post.is_delete, False)
    self.assertEqual(other_post.is_delete, False)

    [...] # delete_post 삭제 처리를 위한 view의 delete_post함수 호출

    delete_post.refresh_from_db()
    other_post.refresh_from_db()

    self.assertEqual(delete_post.is_delete, True)
    self.assertEqual(other_post.is_delete, False)
```   
