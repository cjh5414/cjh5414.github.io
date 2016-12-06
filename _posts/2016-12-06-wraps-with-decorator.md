---
layout: post
title:  "python decorator에 @wraps를 사용하는 이유"
date:   2016-12-6
categories: python
---

<br>  

> python decorator를 구현하기 위해 구글링을 해보는 중에 decorator함수에 @wraps를 사용하는 것을 자주 볼 수 있었다. @wraps를 사용 하는 것과 사용 하지 않는 것의 결과는 차이가 없었는데 왜 사용하는지 이유가 궁금해서 찾아보았다. 결론부터 말하자면 decorator를 이용하면 debugging이 어려워 지는데 @wraps를 사용하면 그 문제를 해결할 수 있다.  

<br>  

## 참고자료  

<https://artemrudenko.wordpress.com/2013/04/15/python-why-you-need-to-use-wraps-with-decorators/>
