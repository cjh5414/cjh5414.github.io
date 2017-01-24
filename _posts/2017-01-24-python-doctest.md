---
layout: post
title:  "Python doctest"
date:   2017-01-24
categories: python
---

<br/>  

> python 표준 라이브러리 중에 `doctest` 라는 것이 존재한다. documentation string(docstring) 안에 포함된 코드를 실행하고 예상 결과를 확인함으로써 코드를 테스트 할 수 있다. 사용법이 매우 간단하며 docstring에 예상 결과 코드를 넣는 것으로 함수의 설명을 대신하는 것과 동시에 코드를 테스트해 볼 수도 있는 장점이 있다. 그러나 코드를 테스트함에 있어서는 코드가 복잡 해짐에 따라 `doctest` 보다는 `unittest` 를 사용하는 것이 더 체계적이고 정확할 것이다.  

<br/>  

## Simple example  

### Source code  

아래와 같은 코드를 넣어서 _doctest_simple.py_ 라는 파일을 생성한다. `my_function(a, b)` 은 a와 b의 곱을 반환하는 함수이고 실행 했을 때 예상되는 결과에 대한 `2*3`, `'a'*3` 두 가지 경우를 docstring에 작성했다.  

_doctest_simple.py_  

```python
def my_function(a, b):
    """Returns a * b.

    Works with numbers:

    >>> my_function(2, 3)
    6

    and strings:

    >>> my_function('a', 3)
    'aaa'
    """
    return a * b
```  

<br/>  

### Run  

작성한 코드를 doctest로 실행해보기 위해 아래와 같은 명령어로 실행한다.  
`-m` 옵션으로 doctest를 메인 프로그램으로 설정하고,  
`-v` 옵션으로 테스트 실행 중에 output이 없는 기본 설정을 결과를 출력하는 것으로 바꿔준다.  

```
$ python -m doctest -v doctest_simple.py
```

<br/>  

### Result  

doctest 는 `>>>` 으로 시작하는 명령문을 찾아서 실행시키고 아래의 예측 값과 비교하여 결과를 알려준다. 이 때, docstring 에 작성된 `Returns a * b.`, `Works with numbers:`, `and strings:` 와 같은 text들은 doctest에 의해 무시되고 테스트 결과에 영향을 주지 않는다.  

```
Trying:
    my_function(2, 3)
Expecting:
    6
ok
Trying:
    my_function('a', 3)
Expecting:
    'aaa'
ok
1 items had no tests:
    doctest_simple
1 items passed all tests:
   2 tests in doctest_simple.my_function
2 tests in 2 items.
2 passed and 0 failed.
Test passed.
```  

<br/>  

## 참고자료  

<https://pymotw.com/2/doctest/>  
