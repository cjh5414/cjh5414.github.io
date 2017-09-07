---
layout: post
title:  Python for 문 index, value 동시에 접근하기
tags:   ['Python']
---

> C, C++, Java 등의 언어에서는 i, j 와 같이 index 변수를 가지고 반복문을 사용한다. Python에서는 리스트(list), 딕셔너리(Dictionary) 등에 접근할 때 `for value in arr:` 와 같이 index 변수를 사용하지 않고도 편리하게 사용할 수 있다. 하지만 종종 value 와 함께 index 값이 필요한 경우가 있다.  

<br/>  

## index 변수를 추가하는 방법  

자료형 크기의 `range`를 이용하면 index로 접근할 수 있다.

```python
arr = [2, 7, 10]
for idx in range(len(arr)):
    print(idx, arr[idx])

# Output:
# 0 2
# 1 7
# 2 10
```    

하지만 python 에서 이런 방식을 사용하기에는 만족스럽지 못하다.  

<br/>  

## `enumerate()` 내장 함수를 이용하는 방법  

`enumerate()`라는 내장 함수를 이용하면 더 편리하고 가시적인 코딩을 할 수 있다.  

```python
arr = [2, 7, 10]
for idx, val in enumerate(arr):
    print(idx, val)

# Output:
# 0 2
# 1 7
# 2 10
```     

<br/>  

## enumerate

`enumerate`는 '열거하다'라는 뜻이다.  
`enumerate()` 내장 함수는 순서가 있는 자료형(tuple, list, string)을 첫번째 인자로 받아서 각각의 index 값과 value 값들을 enumerate 객체로 리턴한다.  

```python
my_list = ['apple', 'banana', 'grapes']
counter_list = list(enumerate(my_list))
print(counter_list)

# Output: [(0, 'apple'), (1, 'banana'), (2, 'grapes')]
```

<br/>  

두번째 인자로 정수를 전달하면 시작하는 index 값을 조정할 수도 있다.   

```python
arr = [2, 7, 10]
for idx, val in enumerate(arr, 5):
    print(idx, val)

# Output:
# 5 2
# 6 7
# 7 10
```   
