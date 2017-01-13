---
layout: post
title:  "이미 push된 file .gitignore 적용하기"
date:   2017-01-14
categories: git
---

> git을 사용할 때 git으로 관리하고 싶지 않은 파일들은 .gitignore 에 명시함으로써 관리하지 않도록 설정할 수 있다. 하지만 종종 무시할 파일을 .gitignore 에 추가하기 전에 git push 해버리는 경우가 있다. 이 때 뒤늦게 .gitignore 을 수정하여 push를 하지만 원격 저장소에서 해당 파일은 사라지지 않는다.  

<br/>  

## Apply .gitignore   

아래의 git 명령들을 실행해주면 .gitignore의 파일들이 적용되어 원격 저장소에서 사라진다.  

```
$ git rm -r --cached .
$ git add .
$ git commit -m "Apply .gitignore"
$ git push
```

<br/>  

## 같은 실수를 반복하지 않기 위한 git 사용 습관  

좋은 git commit 습관을 들이면 이런 문제들을 방지할 수 있다.

### 1. git status  

`$ git status` 명령으로 git으로부터 추적되고 있는 파일과 새로 추가된 파일들을 확인한다.  
추적되고 있는 파일 중에 수정이 일어났으면 `Changes not staged for commit:` 아래에
`modified : file_name`과 같이 표시된다.  

새로 추가된 파일이 있으면 `Untracked files:` 아래에 파일명이 표시된다.  

<br/>  

### 2. .gitignore  

git으로 관리하기를 원하지 않는 파일은 .gitignore에 추가한다.  

```
$ echo ".cache/" >> .gitignore
```  

다시 `$ git status` 명령을 실행하면 `modified: .gitignore`이 추가되었다.  
의미있는 단위의 commit을 위해 .gitignore 변경 사항을 먼저 commit 한다.  

```
$ git add .gitignore
$ git commit -m "Add file_name on .gitignore"
```  

<br/>  

### 3. git diff  

다시 `$ git status` 명령을 실행해 보자.  

_selenium-test.py_ 이 수정되었는데 `$ git diff selenium-test.py` 명령으로 내가 어떤 내용을 수정했는지 제대로 한 것은 맞는지 확인해본다. (뒤의 파일명을 제거하고 `$ git diff` 명령만 입력하면 아직 add 하지 않은 모든 파일의 변경사항을 출력해준다.)  

-빨간색으로 표시된 부분은 삭제 된 내용이고 +초록색으로 표시된 부분은 추가 된 내용이다.  

<br/>  

### 4. git add _file_  

.gitignore 를 commit 하는 과정에서도 봤지만 commit을 하기 전에 무조건 `$ git add .`명령을 사용하는 것은 좋지 않은 습관이다. 원하지 않는 파일들도 함께 원격 저장소에 포함될 수 있다. 내가 수정한 파일이 맞는지 확인하고 하나씩 git add 하는 습관을 들이는 것이 좋다.  

아래의 예시에서 보면 수정된 _selenium-test.py_ 과 새로 추가된 _test.py_ 이 있다.  

모두 내가 작업한 파일들이 맞는지 확인 했으니 git add 해준다.  

```
$ git add selenium-test.py
$ git add test.py
```  

<br/>  

### 5. git commit  

`$ git status` 명령으로 다시 확인해 보면 두 파일의 위치가 `Changes to be committed:` 으로 변경 되었고 commit 할 준비가 되었다. `$ git commit` 명령을 실행하면 vi를 사용하여 commit message를 작성하는 창으로 전환되고 메세지 작성 후에 `:wq` 저장 종료하면 commit이 완료된다.  
`Changes to be committed:`에 존재하는 파일들만 commit이 되고 add하지 않은 파일들은 commit 되지 않는다.  
`$ git commit -m "commit message"` 을 이용하면 commit 명령과 동시에 commit message를 입력할 수 있다.  

<br/>  

## SCM Breeze  

[SCM Breeze](https://github.com/scmbreeze/scm_breeze)라는 터미널에서 git을 편리하게 사용하도록 도와주는 tool이 존재한다.  
git 명령들을 사용하는 중에 다양한 색깔과 추가 메세지로 더 가독성 있게 보여주고 git 명령어들의 alias를 지정해줘서 `git status`, `git add` 등의 명령어를 `gs`, `ga`와 같이 사용할 수 있다.  
또한 변경이 있는 file들에게 번호를 부여하여 `ga 1`, `gd 1` 와 같이 git 명령어에 파일명을 입력하지 않고 번호로 편리하게 조작할 수 있다.   
