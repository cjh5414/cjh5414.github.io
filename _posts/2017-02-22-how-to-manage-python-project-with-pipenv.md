---
layout: post
title:  Pipenv으로 Python 프로젝트 관리하기
tags:   [Python]
---

> [Pipenv에 관한 글](https://robots.thoughtbot.com/how-to-manage-your-python-projects-with-pipenv)이 있어서 읽어봤는데 `requirements.txt`와 `requirements-dev.txt` 을 분리하여 사용하는 것과 패키지의 변경이 있을 때마다 매번 `requirements.txt`를 업데이트 해줘야한다는 번거로움에 대해서 공감이 됐고, 그 번거로움을 해소해준다는 점에 흥미로워서 자세히 읽어보고 번역하게 되었다. 실제로 따라서 사용해보는 과정에서의 자료도 추가했다.  

Python 프로젝트에서 일반적으로 dependencies를 관리하기 위해 Virtualenv을 이용한다.
- ([Python 개발 환경 구축하기 - pyenv, virtualenv, autoenv](https://cjh5414.github.io/python-%EA%B0%9C%EB%B0%9C%ED%99%98%EA%B2%BD%EA%B5%AC%EC%B6%95/))    

가상환경을 생성하여 requirements.txt에 패키지 리스트를 저장하고 다른 작업환경에서 install하면 개발환경을 동일하게 유지할 수 있다. 하지만 일관된 프로젝트 환경을 위해 수동으로 특정 버전의 패키지를 설지, 제거해야하고 매번 `requirements.txt`를 갱신해줘야하는 단점이 있다. 또한, 프로젝트에는 꼭 필요하지 않지만 개발에 편리함을 주기 위한 패키지들을 가상환경에 추가하고 싶을 때가 있는데, 이러한 경우 development environment의 `requirements-dev.txt` 파일과,  production environment의 `requirements.txt` 파일으로 나눠서 이용하는데 번거로움이 있다.  

pipenv는 이런 불편함을 해소하여 파이썬 기반 프로젝트의 dependencies를 관리를 더 간단하게 할 수 있도록 해준다.  

<br/>  

## Getting Started  

pip를 이용하여 [Pipenv](http://docs.pipenv.org/en/latest/)를 설치한다.  

```
$ pip install pipenv
```  

python project가 있는 폴더로 이동한 후 Pipenv를 시작하는 명령을 실행한다.  

```
$ cd my_project
$ pipenv install
```  

맨 처음 프로젝트를 시작하는 것이라면 현재 위치에 [Pipfiles](https://github.com/pypa/pipfile)(`Pipfile`, `Pipfile.lock`) 라는 파일이 생성되면서 새로운 가상환경이 생성될 것이고, 기존의 가상환경이 존재한다면 Pipfiles을 통해서 그 환경으로 설정된다. 명령어 뒤에 `--two`, `--three` 옵션을 이용하면 프로젝트에 사용하는 Python version을 설정할 수 있고, 옵션을 주지 않으면 현재 system의 python version으로 설정된다.  

<br/>  

프로젝트를 새로 시작한다고 가정하고, `pipenv_example`이라는 폴더를 만들고 python3의 가상환경을 생성했다.  

![create env](/images/pipenv/create env.png)  

`Pipfile`, `Pipfile.lock` 라는 두 파일이 생성되었다.  

_Pipfile_  

```
[[source]]
url = "https://pypi.python.org/simple"
verify_ssl = true
```  

_Pipfile.lock_  

```
{
    "default": {},
    "develop": {},
    "_meta": {
        "sources": [
            {
                "url": "https://pypi.python.org/simple",
                "verify_ssl": true
            }
        ],
        "requires": {},
        "hash": {
            "sha256": "cb65967bdf3a7372749ce6144be4ee63f412aeeac624342fda3a2b58b1c43c15"
        }
    }
}
```  

<br/>  

## Python dependencies 관리  

Pipenv는 Python 프로젝트에서 일반적으로 사용하는 `requirements.txt` 와 같이 [Pipfiles](https://github.com/pypa/pipfile)(`Pipfile`, `Pipfile.lock`)에 프로젝트의 dependenscies에 대한 정보를 포함한다. 만약 `requirements.txt` 파일이 존재하는 프로젝트에서 Pipenv를 시작했다면, `requirements.txt` 파일을 지우기 전에 리스트화 되어있는 모든 패키지들을 Pipenv로 설치해줘야 한다.  

프로젝트에 Python 패키지를 설치하기 위해 `install` 키워드를 사용한다. 예를 들면,  

```
$ pipenv install beautifulsoup4
```  

이 명령은 최신 버전의 [Beautiful Soup](https://www.crummy.com/software/BeautifulSoup/) 을 설치한다.  
반대로 패키지를 제거하고 싶다면 `uninstall` 키워드를 이용하여 아래와 같이 하면 된다.  

```
$ pipenv uninstall beautifulsoup4
```  

이렇게 설치한 패키지들은 `lock` 키워드를 이용하면 패키지 이름과 버전이 `Pipfile.lock` 파일에 업데이트 된다. `pip freeze > "requirements.txt"` 와 비슷한 역할이다.  

```
$ pipenv lock
```  

[Pipfiles](https://github.com/pypa/pipfile)(`Pipfile`, `Pipfile.lock`)은 Git repository에 추가하는 것이 좋다. 만약 다른사람이 repository을 clone 하면 Pipenv을 해당 시스템에 설치하고 아래의 명령만 실행하면,

```
$ pipenv install
```  

Pipenv는 자동으로 Pipfiles를 찾아서 새로운 가상 환경을 만들고 필요한 패키지들을 설치한다.   


<br/>  

위에서 생성한 pipenv_example 프로젝트에서 `beautifulsoup4` 패키지를 pipenv로 설치한 결과이다.  

![install package](/images/pipenv/install package.png)  

_Pipfile_  

```
[[source]]
url = "https://pypi.python.org/simple"
verify_ssl = true

[packages]
beautifulsoup4 = "*"
```  

[packages] 부분에 `beautifulsoup4` 패지키가 설치된 것을 확인할 수 있다.  

이어서 `$ pipenv lock` 명령으로 _Pipfile.lock_ 파일을 업데이트 했다.  

![lock](/images/pipenv/lock.png)  

_Pipfile.lock_  

```
{
    "default": {
        "beautifulsoup4": {
            "version": "==4.5.3",
            "hash": "sha256:0a91347d5a4ab2196407ff4d3d758f2e712cae9bdfa3fd1eb0f83edea95e0d8d"
        }
    },
    "develop": {},
    "_meta": {
        "sources": [
            {
                "url": "https://pypi.python.org/simple",
                "verify_ssl": true
            }
        ],
        "requires": {},
        "hash": {
            "sha256": "05f3424257e382f45b0be981329a332c54f856079581433bd71619d8da181aa6"
        }
    }
}
```  

"default" 부분에 `beautifulsoup4` 패지키에 대한 정보가 추가된 것을 확인할 수 있다.  

<br/>  

## 개발 환경 관리  

개발을 하다보면 프로덕션 환경에서는 사용하지 않지만 개발할 때만 사용하는 패키지들이 있다. 예를들면 [unit testing packages](http://docs.python-guide.org/en/latest/writing/tests/) 같은 것들이다. Pipenv는 `--dev` 옵션을 이용하면 두 가지 환경을 분리할 수 있다.  

```
$ pipenv install --dev pytest
```  

pytest 패키지가 설치되지만 오직 개발 환경에서만 연관이 있다. 이렇게 환경을 따로 관리하는 것은 매우 유용하다. 만약 프로덕션 환경의 프로젝트를 이용하고 싶다면 아래와 같이 실행하면 개발 환경의 pytest 패키지는 설치되지 않는다.

```
$ pipenv install
```  

반대로 프로젝트를 clone 해서 개발 환경으로 설정하고 싶다면 `--dev` 옵션을 이용하면 된다.  

```
$ pipenv install --dev
```   

이렇게 하면 개발 패키지들을 포함하여 모든 dependencies 를 설치한다.   

<br/>  

`--dev` 옵션을 이용하여 `pytest` 라는 테스트 패키지를 설치한 결과이다.  

![install dev package](/images/pipenv/install dev package.png)  

_Pipfile_  

```
[[source]]
url = "https://pypi.python.org/simple"
verify_ssl = true

[dev-packages]
pytest = "*"

[packages]
beautifulsoup4 = "*"
```   

이번에는 [dev-packages] 부분에 `pytest` 패키지가 추가되었다.  

_Pipfile.lock_  

```
{
    "default": {
        "beautifulsoup4": {
            "version": "==4.5.3",
            "hash": "sha256:0a91347d5a4ab2196407ff4d3d758f2e712cae9bdfa3fd1eb0f83edea95e0d8d"
        }
    },
    "develop": {
        "pytest": {
            "version": "==3.0.6",
            "hash": "sha256:da0ab50c7eec0683bc24f1c1137db1f4111752054ecdad63125e7ec71316b813"
        }
    },
    "_meta": {
        "sources": [
            {
                "url": "https://pypi.python.org/simple",
                "verify_ssl": true
            }
        ],
        "requires": {},
        "hash": {
            "sha256": "1c5395a7c584b70fc8662231e4e8ea229f42139d2e5925e8b0f58b6b3da863a8"
        }
    }
}
```  

`$ pipenv lock` 명령으로 _Pipfile.lock_ 파일을 업데이트 하면 "develop" 부분에 `pytest` 패키지에 대한 정보가 저장된다. (`pytest` 패키지를 설치하면 추가로 설치되는 패키지들이 몇개 더 있는데 위에서는 표시하지 않았다.)  

<br/>  

## 코드 실행  

가상환경을 활성화시키기 위해서는 `shell` 키워드를 사용한다.  

```
$ pipenv shell
```  

`run` 키워드를 이용하면 가상환경을 활성화하지 않아도 shell 명령으로 실행할 수도 있다.  

```
$ pipenv run which python
```  

`which python` 명령을 가상 환경에서 실행하고 `python` 실행 파일이 있는 경로를 보여준다. (가상 환경의 python 경로가  보여진다.) `run`을 이용한 기능은 가상 환경에서 간단하게 python 코드를 실행하는 방법이다.  

```
$ pipenv run python my_project.py
```  

이런 방식이 마음에 들고 자주 사용할 예정이라면 아래와 같이 alias를 설정하여 사용하는 것을 권한다.  

```
alias prp="pipenv run python"
```  

<br/>  

## virtualenv와 비교  

virtualenv와 비교했을 때 Pipenv의 가장 큰 장점은 여러개의 requirements 파일들을 Pipfiles 으로 대체할 수 있다는 것이라고 생각된다.  

실제로 사용해본 결과 Pipenv를 사용했을 경우 새로운 패키지를 추가하는 과정에서 이점이 있었고 production과 development의 환경을 분리하여 사용하는 데에 있어서 조금 더 편리하긴 하지만 큰 차이는 없었다.  

### 새로운 패키지를 설치하는 경우

Virtualenv에서는 패키지를 설치한 후에 production, development에 따라서 requirements을 선택하여 업데이트 해줘야한다.  

Pipenv에서는 패키지를 설치하는 시점에서 `--dev` 옵션으로 설치 될 환경을 지정하고 production, development에 관계 없이 `$ pipenv lock` 명령 하나로 환경을 분리하여 간단하게 freeze 할 수 있다.  

### 가상 환경으로 설정하는 경우  

Pipenv에서는 가상 환경으로 설정 할 때 `--dev ` 옵션으로 두 가지 환경을 간단하게 분리하는 장점이 있었지만 virtualenv을 사용할 때도 설정 하나만 추가해주면 동일한 효과를 볼 수 있다.  

실제로 현재 진행중인 프로젝트에서는 `requirements-dev.txt`에 `pip install -r requirements.txt` 쉘 명령을 추가해놓음으로  

- development 환경으로 설정 : `pip install -r requirements-dev.txt`   
- production  환경으로 설정 : `pip install -r requirements.txt`  

으로 쉘 명령 하나로 환경 설정을 하고 있다.  

아직 지식의 깊이가 얕아서 모르는 부분에서 더 많은 차이점이 있을 것이라 생각된다. 각자의 장단점에 대해 더 공부해보고 알게되면 추가로 포스팅할 예정이다.  
