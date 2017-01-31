---
layout: post
title: "Django pagination 모듈 선택 및 구현 과정"
tags:  [Django]
---

<br/>  

django를 사용하여 진행 중인 프로젝트에 pagination 기능이 필요해졌다. django pagination 을 검색해보니 [Django Documentation Pagination](https://docs.djangoproject.com/en/1.10/topics/pagination/) 에 내부 모듈로 Paginator을 이용하여서 구현하는 방법이 나와있었다.

예제를 만들어서 테스트 해봤는데 간단해서 금방 사용법을 익힐 수 있었다.  

![page1](/images/pagination/page1.png)   

하지만 docs에 나와있는 예제는 이전 페이지와 다음 페이지만 표시해주기 때문에 실제로 사용하기에는 기능이 부족했다.

내가 원하던 pagination이란

![pagination_example](/images/pagination/pagination_example.png)

이런식으로.. 실제로 사용할 수 있는, 조금 더 사용자가 페이지를 쉽게 넘기고 볼 수 있도록 해주는 것을 원했다.  

`django/core/paginator.py`의 기능들을 더 살펴보고 구현하면 되겠다는 생각이 들었지만 누군가 만들어 놓은 외부 모듈을 사용하면 더 쉽고 빠르게 만들 수 있을거란 생각에 django pagination 패키지들을 찾아봤다.  

- django EL(Endless) pagination  
- django-bootstrap-pagination  
- django-pure-pagination  

등 많은 패키지들이 있었는데 그 중에 view 수가 많고 최근까지 업데이트가 된 (다른 것들에 비해) 'django EL pagination'을 사용해봤다. 마찬가지로 [django EL pagination docs](http://django-el-pagination.readthedocs.io/en/latest/) 에 있는 예제들을 따라해봤다.  

![digg1](/images/pagination/digg1.png)   

![twitter](/images/pagination/twitter.png)   

예제의 양이 많지는 않았지만 django 버전 문제로 인해 시간이 꽤 걸렸다.   

> 예제 사용 방법과 문제점 해결 방법 등 구체적인 내용은 [여기](https://cjh5414.github.io/django-pagination/)에 작성 돼 있다.  

<br>  

## 외부 패키지 장점  

외부 패키지를 실제 프로젝트에 적용하여 사용한다면,  

- 페이스북처럼 scroll을 이용하여 pagination를 할 수도 있다.  

- Digg 등의 이미 구현되어있는 pagination 스타일을 이용할 수 있다.  

- [django-pure-pagination](https://github.com/jamespacileo/django-pure-pagination) 같은 경우는 django의 paginator를 확장하여 만든 것으로 내가 원했던 (페이지 리스트를 보여주고 이전, 다음 등의 버튼이 구현되어 있는) 실제 프로젝트에 적용하면 유용하게 쓸 수 있을 것 같은 패키지도 있었다.    


<br>  

## 외부 패키지 단점  

하지만 외부의 패키지를 가져다 쓰는 것이  

- 버전 문제 등의 여러가지 문제가 존재할 수 있다. (실제로 예제를 돌려보면서 django의 최신버전과 맞지 않아서 문제가 있었다.)  

- 사용법을 익히는 노력이 필요하다. (외부 패키지를 가져다 쓰는 것이 쉽지만은 않다.)  

- 실제 프로젝트에 적용했을 때 오류가 생기면 쉽게 해결하기 어렵다는 불안감도 존재한다.  

- License 문제도 생각해봐야 한다.  

<br/>   

## 결론  

'이미 만들어진 것들을 잘 가져다 쓰는 것도 실력', '누군가 나보다 잘 만들어 놨을 것이다.' 등의 말들이 떠올라서 외부 모듈을 찾아보기 시작했지만..

외부 패키지를 이용할 때 우려되는 점들이 걸렸고, 무엇보다 외부 패키지를 적용하여 많은 기능들을 한번에 이용하려 하는 것은 TDD 기반으로 진행하고 있는 현재 프로젝트에서 '최소한의 동작하는 기능을 빨리 구현하고 나중에 수정해도 된다'는 원칙에서 벗어난다는 생각이 들었다.  

결국 django에서 제공하는 내부의 paginator를 활용하여 간단한 pagination부터 구현하고 나중에 기능을 추가하는 것으로 결정하였다.  
(외부 모듈을 찾아보고 테스트 하는 시간에 직접 구현 했으면 벌써 다 했겠다는 생각이 들어서 시간이 조금 아까웠지만..)

실제로 구현해본 결과 이전, 다음 페이지의 번호를 구하는 수식 등의 몇 가지 복잡한 작업만 잘 처리하면 금방 구현할 수 있었다.  
앞으로 post 뿐만 아니라 comment, board 등 다른 기능에도 pagination을 붙일 수 있도록 템플릿화 하는 과정이 필요하지만 문제 없을 것 같다.  
