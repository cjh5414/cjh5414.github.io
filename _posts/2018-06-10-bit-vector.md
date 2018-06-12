---
layout: post
title:  비트 벡터(Bit Vector) 이용하기
tags:   ['Algorithm']
---

> 비트 벡터를 이용하면 메모리 사용을 크게 줄일 수 있습니다. 예제를 통해 비트 벡터를 어떻게 사용하는지와 어떤 장점이 있는지를 알아봅시다.  

<br/>  

## 비트 벡터란   

비트 벡터란 중복되지 않는 정수 집합을 비트로 나타내는 방식입니다.  
정수 집합 {1, 2, 6}를 비트 벡터로 표현해봅시다.  

  정수  | 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | ...
값의 유무 |   | 유 | 유 |  |   |   | 유 |   |
비트 벡터 | 0 | 1 | 1 | 0 | 0 | 0 | 1 | 0 | 0  

위의 예시에서 집합의 최소, 최대 범위가 0에서 7이라면 고작 1Byte의 공간만 사용하여 데이터를 저장할 수 있습니다. 이렇게 비트 벡터를 이용하면 최소의 저장 공간만 이용하는 장점이 있습니다.  

<br/>  

### 비트 연산자  

비트 벡터를 구현하기 위해서는 비트 연산자를 다룰 줄 알아야 합니다. 간단히 살펴봅시다.  

#### `<<` left shift 연산자  
연산자 왼쪽에 위치한 비트 값에 대해 연산자 오른쪽에 위치한 수 만큼 왼쪽으로 이동 시킵니다.  
```
1 << 4
```  


```
0000 0001  ->  0001 0000
```   

연산 과정은 위와 같고 결과 값은 16이 됩니다.  

<br/>  

#### `>>` right shift 연산자  
반대로 right shift 연산자는 오른쪽으로 이동시킵니다.  

```
5 << 4
```  

```
0010 0000  ->  0000 0010
```   

연산 과정은 위와 같고 결과 값은 2이 됩니다.  

<br/>  

#### OR 연산 ( | ) 

```
0 | 0  ->  0
1 | 0  ->  1
0 | 1  ->  1
1 | 1  ->  1
```  

OR 연산은 특정 비트값을 1으로 만들고 싶을 때 사용할 수 있습니다. 바꾸고자 하는 비트 값의 위치에 1, 나머지는 0으로 채운 값으로 OR 연산을 하면 됩니다.  
예를 들어 `0000 0001`를 `0001 1001` 로 바꾸고 싶다면 `0000 0001`에 4,5번 째 값을 1로 바꾸고 싶은 것이므로 `0001 1000` 과 OR 연산을 해주면 됩니다.  

```
0000 0001 | 0001 1000  ->  0001 1001
1 | 24 = 25
```    

<br/>  

#### AND 연산 ( & )

```
0 & 0  ->  0
1 & 0  ->  0
0 & 1  ->  0
1 & 1  ->  1
```  

AND 연산은 OR 연산과 같은 방식으로 특정 비트값을 0으로 만들고 싶을 때 사용할 수 있습니다. 하지만 이번에는 0으로 만들고 싶은 위치에 0을, 나머지는 1로 채워줘야 합니다.    

```
0001 1001 & 1110 0111  ->  0000 0001
```    

그리고 & 연산자는 특정 비트가 1인지 확인하는 데도 이용할 수 있습니다. 원하는 위치에는 1, 나머지는 0으로 채운 값으로 AND 연산을 하면 됩니다. 아래는 4자리 중에서 왼쪽에서 두 번째 값이 1인지 아닌지 확인 해보는 예제입니다.  

```
0001 & 0100  ->  0000    (1 & 4 = 0)
0101 & 0100  ->  0100    (5 & 4 = 4)
```   

즉, 확인하고자 하는 위치의 비트값이 0이면 전체 결과 값 0이 되고, 1이면 위의 예제에서 두번 째의 결과인 4와 같이 확인하고자 하는 비트 위치의 값이 그대로 출력됩니다.  

<br/>   

## 예제  

a부터 z까지의 문자로만 구성된 문자열이 주어질 때, 해당 문자열에 대해 중복되는 문자가 있는지 없는지를 판단하는 코드를 작성해보려고 합니다. 단순히 생각해보면, 문자열 내의 각 문자에 대해 나머지 다른 문자들과 비교해서 같은 값이 하나라도 있으면 '중복 있음', 반복문을 모두 통과하면 '중복 없음' 으로 간단하게 해결할 수 있을 것입니다. 하지만 이렇게 작성한 코드의 시간 복잡도는 문자열의 각각의 문자에 대해 나머지 다른 문자들도 모두 검사해야하므로 O(N²)이 됩니다. (N은 문자열의 길이 입니다.)    

a부터 z까지의 문자로만 구성된다는 특징을 잘 이용하면 메모리를 추가로 조금 사용하기는 하지만 시간적 측면에서 더 개선시킬 수 있습니다. a부터 z까지의 문자는 총 26개. 26 크기의 배열을 이용하여 해당 문자의 사용 여부를 표시하는 것입니다.  

```java
boolean isUniqueChars(String str) {
    boolean[] alphabet_set = new boolean[26];
    for (int i = 0; i < str.length(); i++) {
        int val = str.charAt(i) - 'a';
        if (alphabet_set[val])
            return false;
        alphabet_set[val] = true;
    }
    return true;
}
```  

이렇게 하면 시간 복잡도를 O(N)으로 줄일 수 있습니다. 26이라는 추가적인 공간을 사용 하기는 하지만 상수이므로 공간 복잡도는 O(1)이 됩니다.  

비트 벡터를 이용하면 어떻게 될까요? 위의 코드에서 `alphabet_set` 의 배열을 사용하는 대신에 int형 변수 하나로 처리가 가능해집니다.  

```java
boolean isUniqueChars(String str) {
    int checker = 0;
    for (int i = 0; i < str.length(); i++) {
        int val = str.charAt(i) - 'a';
        if ((checker & (1 << val)) > 0)
            return false;
        checker |= (1 << val);
    }
    return true;
}
```    

### 비트 벡터 코드 설명  

val은 0에서 25까지의 정수 값이 될 수 있으며 a=0, b=1, ..., z=25 의 값을 순서대로 나타냅니다. 따라서 이 경우의 비트 벡터는 26개의 알파벳의 유무를 나태나는 것으로 26개의 비트가 필요합니다.  

- `1 << val` 는 1을 val 만큼 왼쪽 시프트 연산하여 해당 알파벳을 비트 값으로 표현하는 것입니다. 연산 후의 값은 a는 `... 0001`, b는 `... 0010`, f는 `... 0010 0000`이 됩니다. (참고로 이 값들을 정수로 출력하면 a=1, b=2, c=4, f=32, ... 이 됩니다.)  

- `if ((checker & (1 << val)) > 0)` if문에서는 `checker`와 알파벳의 비트 값을 AND 연산하여 이전에 값이 사용됐는지를 확인합니다. 연산의 결과는 이전에 사용되지 않았으면 0이 되고, 사용됐으면 알파벳에 해당하는 값을 반환하는데 사용이 된 경우의 값은 모두 양수(1, 2, 4, 8, 16, ...)가 됩니다. 따라서 0보다 크다는 조건으로 사용여부를 판단합니다.   

- `checker |= (1 << val);` OR 연산으로 해당 알파벳의 비트 값을 1로 설정합니다.  

<br/>  

> 위의 예제에서는 설명을 위해 간단한 코드를 사용하여 성능 차이가 별로 없지만 상황에 따라서 잘 사용하면 메모리의 효율을 크게 증가시킬 수 있을 것입니다.   

<br/>  

## 참고자료    

- https://www.slideshare.net/bage79/ss-45784200