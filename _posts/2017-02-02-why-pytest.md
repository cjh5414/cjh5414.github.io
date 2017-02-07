---
layout: post
title:  왜 pytest를 사용해야 하는가? [작성중]
tags:   [Python, Test]
---

> Python Django를 이용하고 TDD를 적용하는 프로젝트를 시작하기에 앞서 pytest에 대해 공부를 해보려고 한다. 그 전에 [Why I use py.test and you probably should too](http://halfcooked.com/presentations/pyconau2013/why_I_use_pytest.html) 라는 글이 있어서 읽어보고 번역해보았다.  

<br/>  

## 서론  

단위 테스트를 시도해본 적이 있습니까? 매번 당신의 프로젝트에 테스트를 넣으려고 하지만 어디서부터 시작해야 할지 모르진 않았습니까? 이 글은 당신의 모듈, 패키지, 전체 프로젝트를 단위 테스트 하는 것에 대하여 친절하게 소개해줄 것입니다.  

단위 테스트의 장점을 설명하기 위한 기사, 책, 블로그 포스트, 튜토리얼 비디오 등의 매우 많은 자료가 존재합니다. 그러므로 여기에서는 그런 설명을 상세히 하지 않을 것입니다. 단위 테스팅에 대한 입문 튜토리얼이 잘 되어있는 링크를 이 페이지 하단에 넣어두었습니다.  

이 글은 적어도 단위 테스트에 대해 들어봤거나 혹은 과거에 어떤 프로젝트에 테스트를 작성해보려고 시도해봤다는 것을 가정으로 합니다. 만약 테스트를 시작했다가 멈췄거나, 이미 작성한 코드가 잘 동작해서 테스트를 추가하기 어렵다고 생각한다면 이 기사는 당신에게 많은 도움이 될 것입니다.  

<br/>  

## 단위 테스트란 무엇인가?   

몇 가지 기본적인 개념에 대해서만 간단히 검토해보려고 합니다. 단위 테스트는 모듈이나 애플리케이션 안에 있는 개별적인 코드 단위가 예상대로 작동하는지 확인하는 반복적인 행위입니다. 이것은 보통 당신의 코드를 테스트하는 코드의 형태를 취합니다. 테스트 코드를 테스트하는 코드를 작성할 필요는 없습니다.  

단위 테스트의 중요한 특징 몇가지는 테스트들은 서로 분리되어 있고, 실행은 자동화되며 애플리케이션의 같은 부분을 테스트하는 테스트들은 그룹화되어 한 번에 처리된다는 것입니다.    

테스트 코드들은 서로 분리되어 있고 테스트 되고 있는 코드와도 분리되어 있습니다. 이는 문제를 쉽게 찾고 해결하게 해줍니다.  

편의를 위해 단위테스트는 자동화된 경우에만 동작합니다. 보통 애플리케이션 코드보다 테스트 코드가 더 많으며 테스트를 수동으로 실행하는 것은 매우 좋지 않은 생각이기 때문입니다. 모든 테스트를 한 명령어로 실행하는 것이 이상적입니다. 실제로 이 방법은 대부분의 테스트 라이브러리에서 함수나 모듈이 테스트 코드를 발견하고 실행하는 것을 통해 사용되고 있는 접근법입니다.  

특정 기준에 따라 그룹화된 테스트들은 개발자들이 필요할 때 전체 테스트에서 특정 부분만 따로 테스트를 실행하는 것을 가능하게 해줍니다. 또한, 쉽게 테스트를 찾아서 변경하고 추가할 수 있도록 합니다.  

<br/>  

## 왜 단위 테스트를 하는가?  

프로젝트에 단위 테스트를 적용하는 데에는 "내 코드가 동작하는지 확인하는 것"이라는 명백한 이유 외에도 몇 가지 장점이 있습니다.  

단위 테스트는 코드를 "어떻게" 작성하는지 생각하는데 도움을 줍니다. 게다가 "무엇"을 해야하는지에 있어서 구현 선택을 검토하는데 해가 되지 않고 그 선택들이 적절한지 아닌지 알아냅니다. 단위 테스트를 하고 있는 코드들이 다르게 보이기 시작할 것입니다. 주된 효과로는 단위 테스트를 추가하는 것은 애플리케이션의 유닛(함수/메소드)를 더 작게 만듭니다. 이것은 코드를 이해하고 테스트 하기 쉽게 만드며, 따라서 변화시키는 것 또한 쉽도록 합니다. 코드의 단위는 작을수록 좋습니다. 이것에 대한 훌륭한 설명이 있습니다.  

> '많은 일을 하는 테스팅 코드는 어렵다.'  
'많은 일을 하는 디버깅 코드는 어렵다.'  
이 두 가지 문제의 해결법은 많은 일을 하지 않도록 코드를 작성하는 것이다. 각각의 함수를 단 한가지만의 일을 하도록 작성해야 한다. 이렇게 하면 단위 테스트로 쉽게 테스트할 수 있다. (하나의 함수에 대해 많은 단위 테스트가 필요하지 않는다.)  
내 동료가 메소드를 더 작게 분리해야 하는지 판단할 때 사용하는 문구가 있다. 만약 코드의 역할을 다른 프로그래머에게 설명할 때 'and'라는 단어를 사용했다면 그 메소드는 적어도 하나 이상의 부분으로 나눠야 한다는 것이다.  
출처 : [StackOverflow](http://softwareengineering.stackexchange.com/questions/195989/is-it-ok-to-split-long-functions-and-methods-into-smaller-ones-even-though-they/195992#195992)  

단위 테스트의 다른 장점으로는 문제를 빨리 발견하고 변화를 쉽게하며 통합을 간단하게 하고 설계를 개선할 수 있다는 것입니다. 자세한 내용은 [Wikipedia Unit testing](https://en.wikipedia.org/wiki/Unit_testing)를 참고하십시오.  

<br/>  

## 단위 테스트 라이브러리  

파이썬 표준 라이브러리 중에 [_unittest_](https://docs.python.org/3/library/unittest.html) 모듈이 있다. 이것은 Java의 [_junit_](http://junit.org/junit4/)을 기반으로 하고있다. 자바 프로그래머에게는 unittest가 시작점이 될 것입니다.  

그렇지 않은 다른 사람에게는 [_pytest_](http://docs.pytest.org/en/latest/)가 좋은 선택일 것입니다. 테스트에 필요한 boiler plate code(getter, setter와 같이 반복되지만 자주 쓰이는 코드)가 매우 적기 때문에 pytest로 프로젝트 테스트를 시작하는 것은 매우 쉽습니다. pytest를 이용하여 테스트를 시작하는 것은 기본 라이브러리와는 거리가 있지만 빠릅니다. 또한 필요한 도구 및 테스트 기능을 모두 가지고 있습니다. traceback reporting은 환상적이고, 효과적인 test collection과 execution, test skipping과 매개 변수화 된 테스트 작성 기능을 가지고 있습니다. 자세한 내용은 [pytest docs](http://docs.pytest.org/en/latest/)를 참고하십시오.  

내가 pytest를 선택하고 계속 사용하는 이유는 간단한 test collection, 적은 boilerplate와 테스트, 크래스, 모듈 레벨에서의 setup, teardown 함수 정의 기능이다.  

예를 들어 이런 함수가 있을 때,  

```python
def parse_connection(connection_string):
    """Work out the user name and password in connection_string

    Connection string should be of the form 'database://username@password'
    """
    if '@' in connection_string:
        # Username is characters between '://' and '@'
        slash_position = connection_string.find('://')
        at_position = connection_string.find('@')
        user_name = connection_string[slash_position+3:at_position]
        password = connection_string[at_position+1:]
        return user_name, password
```  

[unittest](https://docs.python.org/3/library/unittest.html)로 작성할 수 있는 가장 간단한 테스트코드:  

```python
from parse_conn import parse_connection
import unittest

class InvalidInputs(unittest.TestCase):
    def testNoAt(self):
        """parse_connection should raise an exception for an invalid connection string"""
        self.assertRaises(ValueError, parse_connection, 'invalid uri')

if __name__ == "__main__":
    unittest.main()
```   

[pytest](http://docs.pytest.org/en/latest/)에서 더 간단히 작성할 수 있는 테스트코드:  

```python
from parse_conn import parse_connection
import py.test

def test_not_at():
    py.test.raises(ValueError, parse_connection, 'invalid uri')
```  

_unittest.TestCase_ 가 무엇인지 또는 테스트를 실행시키기 위해 _unittest.main()_ 을 호출해야 하는지를 알 필요가 없습니다.  command line에서 pytest를 아무런 옵션 없이 실행시키면 현재 디렉토리에서 이름이 test로 시작하는 파일을 찾을 것이고 그 파일에서 test로 시작하는 함수를 실행할 것입니다. 물론, 어떤 모듈을 실행할지, 심지어 어떤 테스트를 실행할지에 대해 더 명확히 할 수 있으며 이 특별한 기능은 pytest가 unittest를 앞서는 부분입니다.  

본질적으로 pytest를 unittest보다 선호하는 이유는 테스트 코드를 작성하기 시작할 때 더 적은 작업을 요구하기 때문입니다. 그러나 [unittest2](https://pypi.python.org/pypi/unittest2)와 함께 pytest의 편리함은 표준 라이브러리가 되고 있습니다.  

pytest를 좋아하는 또 다른 이유는 테스트 함수, 클래스, 모듈 레벨에서 set up, tear down 메소드(일관된 테스트 상태를 만들기 위해 사용한다.)를 정의 할 수 있다는 것입니다. 이것은 사실 하나 이상의 테스트는 같은 set up을 공유할 수 있고 잠재적으로 다른 것에 영향을 줄 수 있기 때문에 단위 테스트의 격리 원칙에 위배됩니다. 그러나 적절하게 사용한다면 Zen of the Python 중의 _Although practicality beats purity_ 의 좋은 예가 될 것입니다. 전체 데이터베이스나 큰 랜덤 테스트 데이터 셋을 생성하는 것과 같이 크고 복잡하고 값비싼 set up을 필요로 하는 테스트 함수들이 있다면 테스트 각각의 set up을 반복하는 것은 많은 시간을 소비할 것입니다. 오랜 시간 동작하는 테스트는 자주 실행하면 안됩니다. 다른 함수들을 방해하지 않고 테스트 함수 간에 상태를 공유할 수 있다면 복잡한 set up과 tear down을 공유하는 것은 전체 테스트를 실행하는 데 걸리는 시간을 크게 줄일 수 있습니다.  

<br/>  
