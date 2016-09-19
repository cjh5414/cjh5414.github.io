---
title: Python import
layout: post
---

## import  
import는 '수입하다'라는 뜻으로 python에서는 다른 모듈 내의 코드에 접근을 가능하게 해주는 역할을 한다. import를 사용하는 벙법에는 여러가지 형태가 있다.  

```
import module
from module import submodule
from . import submodule
```  

## 예제
- 파일 구조

```
import_test/
	hello.py
	calc/
		plus_and_minus.py
```  

- hello.py  

```
hello_str = 'Hello!'
```  

- plus_and_minus.py  

```
def plus(a, b):
	return a+b

def minus(a, b):
	return a-b
```  


### python shell을 실행시켜서 몇 가지 형태의 import문을 확인해본다.  

```
$ cd import_test
$ python
```  

```
>>> import hello
>>> hello.hello_str
'Hello!'
```  

```
>>> from hello import hello_str
>>> hello_str
'Hello!'
```  

```
>>> from calc import plus_and_minus
>>> plus_and_minus.plus(1,2)
3
```  


```
>>> from calc.plus_and_minus import plus
>>> plus(1,2)
3
```  

### 별칭 사용  - as  
```
>>> import calc.plus_and_minus as pm
>>> pm.plus(1,2)
3
>>> pm.minus(2,1)
1
```  

## 주의사항  

### PEP8은 다음과 같은 순서로 그룹을 지을 것을 제안한다.  
1. 표준 라이브러리 임포트  
2. 연관 외부 라이버르리 임포트  
3. 로컬 애플리케이션 또는 라이브러리에 한정된 임포트  

### import * 는 피하자.  
코드가 복잡해지고 어떤 모듈이 어떤 함수, 변수를 불러오는지 파악하기 힘들어져서 가독성이 떨어진다.  
또한, import한 다른 파이썬 모듈이 현재 작업하는 모듈의 것을 덮어버리는 문제가 발생 할 수 있기 때문에 위험하다.  

### 명시적 성격의 상대 임포트를 이용하자.  
명시적 성격의 상대 임포트(explicit relative import)를 통해 모듈의 패키지를 하드 코딩하거나 구조적으로 종속된 모듈을 어렵게 분리해야 하는 경우들을 피해 갈 수 있다.

## 참고자료  
Two Scoops of Django - 대니얼 로이 그린펠드, 오드리 로이 그린펠드


