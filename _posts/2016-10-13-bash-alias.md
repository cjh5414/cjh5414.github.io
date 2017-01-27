---
layout: post
title: "bash에서 alias(별명) 이용하기"
tags:  [Git, Linux]
---

<br>  

> git을 사용하다보면 'git branch', 'git status', 'git diff' 등의 고정적으로 자주 사용하는 명령어들이 있다. 겨우 8~10 글자지만 매번 치는 것이 귀찮다. 멘토님께서 gb, gs 등으로 alias를 지정해서 편리하게 사용하는 것을 보고 따라해봤다.

<br>  

## _~/.bash_profile_ 에 alias 추가

```
$ echo "alias _별명_='_원래 명령어_'" >> ~/.bash_profile
$ echo "alias gb='git branch'" >> ~/.bash_profile
$ echo "alias gs='git status'" >> ~/.bash_profile
$ echo "alias gd='git diff'" >> ~/.bash_profile
```

<br>  

## _~/.bash_profile_ 확인   

```
$ cat ~/.bash_profile
alias gb='git branch'
alias gs='git status'
alias gd='git diff'
```  

<br>  

## _~/.bash_profile_ 변경 사항 적용   

```
$ source ~/.bash_profile
```  

혹은 bash 재실행.  


<br>

## 사용   

```
$ gb
  feature/pagination
* feature/pagination2
  master
  modify_PostModelTest

$ gs
 On branch feature/pagination2
 Your branch is up-to-date with 'origin/feature/pagination2'.
 nothing to commit, working directory clean
```  
