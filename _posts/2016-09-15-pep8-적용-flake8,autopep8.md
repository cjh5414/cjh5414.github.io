---
title: PEP8 tools - pep8, flake8, autopep8
layout: post
---

PEP8을 잘 지킬 수 있도록 도와주는 도구들이 많이 있다. 그 중에서 pep8, flake8, autopep8에 대해서 알아본다.

PEP8이 적용되지 않은 예제코드  
```
import sys

def add(x, y):
	return x +y

print(add(1,2))
```

## pep8  
### Installation  
```
pip install pep8
```

### Using  
```
pep8 test.py
```

아래와 같은 결과가 출력된다.  

```
test.py:3:1: E302 expected 2 blank lines, found 1
test.py:4:1: W191 indentation contains tabs
test.py:4:12: E225 missing whitespace around operator
test.py:6:12: E231 missing whitespace after ','
```

3라인에 2라인을 띄어써라.  
tab을 사용하지 마라.  
연산자 주변, ','뒤에 띄어쓰기를 해라.  
등의 결과를 볼 수 있다.

## flake8  
### Installation  
```
pip install flake8
```  

### Using  
```
flake8 test.py
```

결과는 아래와 같다.  

```
test.py:1:1: F401 'sys' imported but unused
test.py:3:1: E302 expected 2 blank lines, found 1
test.py:4:1: W191 indentation contains tabs
test.py:4:12: E225 missing whitespace around operator
test.py:6:12: E231 missing whitespace after ','
```

pep8을 사용했을 때와 마찬가지로 pep8에 위반되는 사항들을 출력해준다.  

- select 옵션으로 특정한 warning, error 메세지만 확인할 수도 있다.  
```
flake8 --select E302 test.py
```  

```
test.py:1:1: F401 'sys' imported but unused
test.py:3:1: E302 expected 2 blank lines, found 1
```

- ignore 옵션으로는 특정한 warning, error 메세지를 무시할 수도 있다.  
```
flake8 --ignore E302 test.py
```  

```
test.py:1:1: F401 'sys' imported but unused
test.py:4:1: W191 indentation contains tabs
test.py:4:12: E225 missing whitespace around operator
test.py:6:12: E231 missing whitespace after ','
```

- `~/.config/flake8`의 환경설정 파일을 통해 79라인 규칙을 100라인으로 늘리는 등의 설정을 변경할 수도 있다.  

## autopep8  
### Installation  
```
pip install autopep8
```

### Using  
- 아래의 명령으로 pep8을 적용한 후의 코드의 상태를 출력할 수 있다.  
```
autopep8 test.py
```
출력결과.  
```
import sys


def add(x, y):
    return x + y

print(add(1, 2))
```

- 옵션을 통해 파일 자체를 pep8을 적용하여 변경시켜준다.  
```
autopep8 --in-place --aggressive --aggressive test.py
```

## 참고자료  
<https://pypi.python.org/pypi/pep8><br>  
<https://pypi.python.org/pypi/flake8><br>
<https://pypi.python.org/pypi/autopep8>



