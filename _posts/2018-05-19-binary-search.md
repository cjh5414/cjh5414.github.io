---
layout: post
title:  이진 탐색(Binary Search) 알고리즘 개념 이해 및 추가 예제
tags:   ['Algorithm']
---

## 이진 탐색이란?

이진 탐색이란 데이터가 정렬돼 있는 배열에서 특정한 값을 찾아내는 알고리즘이다. 배열의 중간에 있는 임의의 값을 선택하여 찾고자 하는 값 X와 비교한다. X가 중간 값보다 작으면 중간 값을 기준으로 좌측의 데이터들을 대상으로, X가 중간값보다 크면 배열의 우측을 대상으로 다시 탐색한다. 동일한 방법으로 다시 중간의 값을 임의로 선택하고 비교한다. 해당 값을 찾을 때까지 이 과정을 반복한다.

<br/>  

## 이진 탐색 예시  

오름차순으로 정렬된 배열이 있다.   

> { 17, 28, 43, 67, 88, 92, 100 }   

이 배열에서 이진 탐색을 이용하여 `43`의 값을 찾아보자.  

<br/>  

### 첫 번째 시도  

우선 가운데에 위치한 임의의 값 `67`을 선택한다.  

선택한 값 `67`과 찾고자 하는 값 `43`를 비교한다.  

`43 < 67` 이므로 `43`은 `67`의 좌측에 존재한다는 것을 알 수 있다.    

<br/>  

### 두 번째 시도  

`67`을 기준으로 좌측에 있는 배열 값들을 대상으로 다시 탐색을 진행한다.   

> { 17, 28, 43 }  

마찬가지로 가운데의 임의의 값 `28`을 선택한다.   

`28 < 43` 이번에는 `28`이 `43`보다 작으므로 `28` 우측에 위치하는 것을 알 수 있다.  

<br/>  

### 세 번째 시도  

`28`의 우측을 기준으로 배열을 다시 설정해보면  

> { 43 }

배열에 값이 하나만 남게 되고 값을 확인해보면,   
`43 == 43` 원하는 값을 찾았다.  

<br/>  

### 종료 조건  

탐색의 종료 조건은 원하는 값을 찾으면 종료된다.  
운이 좋게 한 번에 찾을 수도 있고 위의 예제와 같이 마지막에 찾을 수도 있다.  

만약 원하는 값이 배열이 존재하지 않는다면 어떻게 종료될까?  
방금 살펴본 예제를 그대로 이용하여 `40` 을 찾는다고 가정해보자.  

두 번째 시도까지는 동일하게 진행된다.  

세 번째 시도에서 값을 비교해보면,
`40 < 43` 으로 원하는 값 `40` 보다 작다. 배열의 좌측을 탐색해야 하는데 더 이상 남은 배열이 존재하지 않는다.  
이렇게 탐색하고자 하는 배열이 더이상 존재하지 않으면 찾고자 하는 값이 배열에 존재하지 않는다는 것으로 판단할 수 있고 탐색을 종료한다.  

<br/>  

## 이진 탐색 소스코드 구현 방법  

그렇다면 소스코드로 구현할 경우 어떻게 배열에 접근하고 종료 조건을 판단할 수 있을까?  
인덱스를 이용한다. 인덱스의 최소, 최대 값을 따로 저장하여 탐색이 진행될 때 마다 갱신하고 탐색하는 배열의 범위를 줄여나간다.  

그럼 코드를 살펴보자. 구현 방법은 두 가지가 존재한다.  

### 반복문을 이용한 방법   

```java
int BSearch(int arr[], int target) {
    int low = 0;
    int high = arr.length - 1;
    int mid;

    while(low <= high) {
        mid = (low + high) / 2;

        if (arr[mid] == target)
            return mid;
        else if (arr[mid] > target)
            high = mid - 1;
        else
            low = mid + 1;
    }
    return -1;
}
```  

### 재귀함수를 이용한 방법  

```java
int BSearchRecursive(int arr[], int target, int low, int high) {
    if (low > high)
        return -1;

    int mid = (low + high) / 2;
    if (arr[mid] == target)
        return mid;
    else if (arr[mid] > target)
        return BSearchRecursive(arr, target, low, mid-1);
    else
        return BSearchRecursive(arr, target, mid+1, high);
}
```  

<br/>  

## 이진 탐색 시간 복잡도 및 빅오 표기    

> 빅오 표기법에 대한 공부가 필요하다면 아래 링크를 참고하길 바란다.  
> - [빅오 표기법 (Big-O Notation), 시간 복잡도, 공간 복잡도](https://cjh5414.github.io/big-o-notation/)    

<br/>  

위에서 사용했던 배열에 원소 몇 개를 추가해서 다시 `17` 을 이진 탐색으로 찾아보자.  

> { 17, 28, 43, 67, 88, 92, 100, 200 }     
중간 값 : 88  ->  작다  ->  { 17, 28, 43, 67 }  
중간 값 : 43  ->  작다  ->  { 17, 28 }  
중간 값 : 28  ->  작다  ->  { 17 }  
중간 값 : 17  ->  종료  

이진 탐색이란 이름이 붙여진 이유는 처음에 N개 크기의 배열에서 단계가 하나씩 지나감에 따라 탐색할 배열의 크기가 반씩 줄어들기 때문이다. 위의 예제에서 확인해보면,

맨 처음 원소의 개수가 8에서 4, 2, 1 으로 반씩 점차 줄어드는 것을 확인할 수 있다.
즉, 위의 경우 N=8일 때 탐색이 3회 수행 됐으므로 시간 복잡도는 3이 된다.  

일반화하여 생각해보자. N개의 크기 배열을 이진 탐색하면 N, N/2, N/4, N/8, ... , 1  으로 실행 될 것이다. 여기서 실행된 탐색의 횟수가 시간 복잡도가 될 것이고 그 값을 K라고 한다면, K는 N에 대해 어떻게 나타낼 수 있을까?  
1에 2를 K번 곱하면? N이 된다.  
2^K = N  
K = log2N   

즉, 이진 탐색의 시간 복잡도는 O(logN) 이 된다.   

<br/>

## 추가 예제 - N의 제곱근 구하기  

이진 탐색은 위에서 말했듯이 정렬된 데이터에서 특정한 값을 찾고자할 때 O(logN)의 성능으로 빠르게 값을 찾을 수 있는 장점이 있다.  

양의 정수 N 이 주어졌을 때 N의 제곱근 √N 을 구하는 소스코드를 작성해보자.
단, 제곱근 값이 정수 일 경우에만 출력한다.

예를들어,

N = 100  ->  10  
N = 25   ->   5  
N = 10   ->  -1  
N =  1   ->   1  

와 같이 출력한다.   

단순히 생각해서 0부터 n-1 까지 자연수를 하나씩 증가시켜 가면서 확인해보면 구할 수 있을 것이다.  

```java  
public static int squareRootLoop(int n) {
    for (int i=0; i<n; i++)
        if (i * i == n)
            return i;
    return -1;
}
```   

결과 값이 자연수라면 √N 시간 안에 금방 찾을 수 있지만 자연수가 아닌 경우는 N만큼 확인해봐야 하므로
빅오 표기법으로 O(N) 시간이 된다.  

제곱근의 값은 N/2 보다 작거나 같다는 특징을 이용하여 반복문의 조건을  
`for (int i=0; i<=n/2; i++)` 로 바꾸면 성능이 개선되겠지만 시간복잡도는 여전히 O(N)이다.   

문제 접근을 조금 다르게 해보자.  
우리는 지금 0부터 N까지의 자연수 중에 N의 제곱근이 있는지 없는지를 찾아보고 있다.  
즉, 0부터 N까지의 정렬된 자연수에서 특정 값을 찾고 있는 것이다.   
이진 탐색을 이용하여 코드를 다시 작성한다면 시간 복잡도를 O(logN)까지 줄일 수 있을 것이다.  

```java
public static int squareRootBSearch(int n) {
    int min = 0;
    int max = n;
    int guess;

    while (min <= max) {
        guess = (min + max) / 2;
        if (guess * guess == n)
            return guess;
        else if (guess * guess > n)
            max = guess - 1;
        else
            min = guess + 1;
    }
    return -1;
}
```  
