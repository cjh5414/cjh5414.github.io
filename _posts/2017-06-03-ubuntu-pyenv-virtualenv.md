---
layout: post
title:  Ubuntu에서 pyenv, virtualenv, autoenv 이용하기
tags:   ['Linux', 'Python']
---

> Mac 에서 pyenv, virtualenv, autoenv 를 이용하는 방법은 [여기](https://cjh5414.github.io/python-%EA%B0%9C%EB%B0%9C%ED%99%98%EA%B2%BD%EA%B5%AC%EC%B6%95/)를 참고하길 바란다. Ubuntu에서 이용하는 방법은 설치하는 과정에서만 차이가 약간 있다. 자세한 사용 방법과 개념 설명이 필요할 때에도 위의 링크를 참고하면 좋을 것 같다.  

<br/>  

Python을 사용하면 대부분 가상환경을 이용하는데 서버에서도 마찬가지로 설정을 해줘야한다.  

먼저 Ubuntu에서 Build 할 때 공통적으로 발생하는 문제를 방지하기 위해 필요한 패키지들을 설치해준다.  

```
$ sudo apt-get install -y make build-essential libssl-dev zlib1g-dev libbz2-dev \
libreadline-dev libsqlite3-dev wget curl llvm libncurses5-dev libncursesw5-dev \
xz-utils tk-dev
```  

> 참고로 [pyenv - Common build problems](https://github.com/pyenv/pyenv/wiki/Common-build-problems)에 나와있는 내용이다.  

<br/>  

## pyenv  

git clone 을 이용하여 소스를 다운받고 몇 가지 설정을 해준다.      

```
$ git clone https://github.com/pyenv/pyenv.git ~/.pyenv

$ echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.bash_profile
$ echo 'export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.bash_profile
$ echo 'eval "$(pyenv init -)"' >> ~/.bash_profile
```  

변경한 _~/.bash_profile_ 을 적용하기 위해 `source` 명령을 실행해주고 `pyenv` 설치가 잘 됐는지 확인해본다.  

```
$ source ~/.bash_profile
$ pyenv versions
* system (set by /home/nelp/.pyenv/version)
```  

원하는 python 버전을 설치 해보고 사용해본다.  

```
$ pyenv install 3.5.2
Downloading Python-3.5.2.tar.xz...
-> https://www.python.org/ftp/python/3.5.2/Python-3.5.2.tar.xz
Installing Python-3.5.2...
patching file Lib/venv/scripts/posix/activate.fish
Installed Python-3.5.2 to /home/nelp/.pyenv/versions/3.5.2

$ pyenv versions
* system (set by /home/nelp/.pyenv/version)
  3.5.2

$ pyenv shell 3.5.2
$ python
Python 3.5.2 (default, Jun  4 2017, 05:30:18)
[GCC 5.4.0 20160609] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>>
```  

<br/>  

## pyenv-virtualenv  

`pyenv`와 마찬가지로 clone 받고 설정들을 해준다.  

```
$ git clone https://github.com/yyuu/pyenv-virtualenv.git ~/.pyenv/plugins/pyenv-virtualenv
```  

```
$ echo 'eval "$(pyenv virtualenv-init -)"' >> ~/.bash_profile
```

```
$ source ~/.bash_profile
```  

테스트를 위한 가상환경을 설치하고 사용해보는 과정이다.  

```
$ pyenv virtualenv 3.5.2 test-env
$ pyenv versions
  system
* 3.5.2
  3.5.2/envs/test-env
  test-env (set by PYENV_VERSION environment variable)

$ pyenv activate test-env
(test-env) $ pyenv versions
  system
  3.5.2
  3.5.2/envs/test-env
* test-env (set by PYENV_VERSION environment variable)
(test-env) $ python -V
Python 3.5.2

$ pyenv deactivate
```  

<br/>  

## autoenv  

autoenv를 설정하면 특정 디렉토리로 이동했을 때 자동으로 특정 가상환경으로 activate 되도록 할 수 있다.  

```
$ git clone git://github.com/kennethreitz/autoenv.git ~/.autoenv
$ echo 'source ~/.autoenv/activate.sh' >> ~/.bash_profile
$ source ~/.bash_profile
```  

```
$ mkdir test-dir
$ cd test-dir
$ touch .env
$ echo "pyenv activate test-env" > .env
```

```
$ cd test-dir/
autoenv:
autoenv: WARNING:
autoenv: This is the first time you are about to source /home/test-env/.env:
autoenv:
autoenv:   --- (begin contents) ---------------------------------------
autoenv:     pyenv activate test-env$
autoenv:
autoenv:   --- (end contents) -----------------------------------------
autoenv:
autoenv: Are you sure you want to allow this? (y/N) y
```  
