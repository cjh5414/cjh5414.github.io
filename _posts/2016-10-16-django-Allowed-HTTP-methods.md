---
layout: post
title:  "Django에서 사용하지 않는 GET 접근방식 처리하기 - django Allowed HTTP methods"
tags:   [Django]
---

<br>  

TDD, django를 사용하고 있는 프로젝트에서 게시글 삭제 기능을 구현했다.  
전달할 Data가 없어서 '/delete'라는 URL으로만 GET방식을 사용할 수도 있었지만 'DB 값 수정을 요청하는 기능'이기 때문에 POST방식으로 구현했다.  

```
def delete_post(request, board_slug, post_id):
    if request.method == 'POST':
        [...]
```  

GET방식은 사용하지 않기 때문에 POST에 대해서만 조건문으로 처리를 했다. 하지만 GET방식으로 접근을 하면   

```
The view board.views.delete_post didn't return an HttpResponse object. It returned None instead.
```  

와 같은 에러메세지가 뜬다.  

만약 Delete 기능을 url을 통해 접근하려는 사용자가 있다고 가정하면 이 처럼 GET으로 잘못 접근 했을 때, 이와 같은 에러 페이지는 잘못된 것이다. 잘못 된 접근이라는 메시지를 전달해주면 좋을 것 같다.  

이를 테스트하기 위한 테스트 코드를 먼저 작성한다.  

```
def test_can_not_access_with_GET(self):
    delete_post = Post.objects.create(board=self.default_board, title='delete post', content='content')
    response = self.client.get(reverse('board:delete_post', args=[self.default_board.slug, delete_post.id]))

    self.assertEqual(response.status_code, 405)
```  

delete를 GET으로 호출한 response의 status code가 '허용되지 않는 방법'을 나타내는 405 인지 검사한다.   

_동작 코드를 작성하기 전 실패하는 테스트 결과_  

```
ValueError: The view board.views.delete_post didn't return an HttpResponse object. It returned None instead.
```  

해결 방법은 django의 [Allowed Http methods](https://docs.djangoproject.com/en/1.10/topics/http/decorators/#allowed-http-methods)를 사용하면 간단하게 처리할 수 있다. 해당 함수 위에 decorators만 추가 해주면 된다.

```
@require_POST
def delete_post(request, board_slug, post_id):
    if request.method == 'POST':
        [...]
```  

테스트를 다시 돌려보면 통과한다. `@require_POST`를 추가하면 POST를 제외한 다른 접근에 대해서는 빈 페이지와 405코드를 반환한다. 자세한 내용은 [여기](https://docs.djangoproject.com/en/1.10/topics/http/decorators/#allowed-http-methods)를 참고하면 된다.


빈 페이지가 아니라 Error 메세지에 대한 Page를 만들어서 반환하고 싶다면 else 문을 추가하여 GET방식으로 접근했을 때 반환하는 페이지를 설정하면 된다.  
