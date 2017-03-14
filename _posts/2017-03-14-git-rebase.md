---
layout: post
title:  git rebase로 commit 합치기
tags:   [Git]
---

> Git을 사용하다보면 commit을 한 후에 누락된 작업이 생각이 나거나 이미 커밋한 내용 중에 수정이 필요한 경우가 종종 생기는데, 추가로 작업한 후에 다시 commit을 하면 동일한 이름의 commit 혹은 의미없는 새로운 commit이 생기게 된다. 이때, `git rebase` 명령을 이용하면 변경사항은 적용하면서 이전과 현재의 커밋을 합칠 수 있다. `git rebase` 명령 중에서도 `squash(s)`를 자주 이용했었는데 단순히 현재 commit의 log message를 지우고 과거의 commit으로 합치는 것이라면 `fixup(f)`을 이용하는 것이 더 편리하다.  

<br/>  

### git rebase  

`git rebase -i HEAD~N` 명령을 이용하면 현재 commit으로부터 N개의 commit에 대해 commit 합침, 제거, 메세지 수정 등이 가능하다. 명령을 실행하면 다음 단계에서 N개의 commit에 대해 각각의 간략한 해시 값과 메세지를 보여주며 맨 앞에 `pick`, `squash` 등의 명령을 이용할 수 있다. `pick` 명령이 기본 값으로 설정되어 보여지며, `Commands:` 를 살펴보면 각각의 명령들에 대한 설명이 간단하게 나와있다. 이용하는 방법은 `pick` 자리에 원하는 명령을 입력하면 된다. (p, s, f, d 등의 약어를 사용하면 편리하다.) 아래의 예시를 통해 자세히 살펴보자.  

<br/>  

### Example  

아래와 같은 commit log가 있다. `second commit` 이 의미없는 커밋이기 때문에 `first commit`으로 합치고 싶은 상황이다.    

```
$ git log --oneline
ae53bdf second commit
004644d first commit
[...]
```  

<br/>  

### Squash를 이용한 rebase  

최근 2개의 commit을 작업하기 위해 아래와 같은 명령을 입력한다.  

```
$ git rebase -i HEAD~2
pick 004644d first commit
pick ae53bdf second commit

# Rebase f42adb4..ae53bdf onto f42adb4 (2 commands)
#
# Commands:
# p, pick = use commit
# r, reword = use commit, but edit the commit message
# e, edit = use commit, but stop for amending
# s, squash = use commit, but meld into previous commit
# f, fixup = like "squash", but discard this commit's log message
# x, exec = run command (the rest of the line) using shell
# d, drop = remove commit
#
# These lines can be re-ordered; they are executed from top to bottom.
#
# If you remove a line here THAT COMMIT WILL BE LOST.
#
# However, if you remove everything, the rebase will be aborted.
#
# Note that empty commits are commented out
```  

`seconde commit`을 `first commit`에 합치기 위해 `squash` 명령을 이용하여 적용한다.     

```
pick 004644d first commit
s ae53bdf second commit
```   

`:wq`으로 저장 후 종료하면,  

```
# This is a combination of 2 commits.
# This is the 1st commit message:

first commit

# This is the commit message #2:

second commit

# Please enter the commit message for your changes. Lines starting
# with '#' will be ignored, and an empty message aborts the commit.
#
# Date:      Tue Mar 14 21:50:01 2017 +0900
#
# interactive rebase in progress; onto f42adb4
# Last commands done (2 commands done):
#    pick 004644d first commit
#    s ae53bdf second commit
# No commands remaining.
# You are currently editing a commit while rebasing branch 'master' on 'f42adb4'.
#
# Changes to be committed:
#   modified:   test.txt
#
```  

vi를 통해 commit message 를 변경할 수 있다. 지우고 싶은 commit message를 `#`으로 주석처리 할 수 있고, 수정할 수도 있다.  

```
$ git log --oneline
98a0a97 first commit
[...]
```  

2개만 합칠 수 있는 것이 아니라 여러개의 commit을 squash로 지정할 수 있다. 또한 반드시 현재의 commit을 이전의 commit으로 합쳐야 하는 것이 아니라 합쳐지고 싶은 commit에 대해 `squash(s)` 를 지정하면 된다.  

<br/>  

### fixup을 이용한 rebase   

하지만 단순히 현재 commit(`second commit`)을 이전 commit(`first commit`)으로 합치고 싶은 것이라면 `fixup(f)`을 이용하는 것이 편리하다.  

```
$ git rebase -i HEAD~2
```  

명령을 실행 한 후에   

```
pick 004644d first commit
f ae53bdf second commit
```    

와 같이 입력하면 이후에 특별한 작업 없이 바로 commit이 합쳐진다. 변경 사항들은 적용되면서 `second commit`은 사라지고 `first commit` 만 남는다.  

<br/>  

### 이외의 기능  

이 외에도 `git rebase`를 이용하면  

- reword(r) : 특정 commit의 message 변경  
- drop(d)   : 특정 commit 제거  

등의 기능도 이용할 수 있다.  
