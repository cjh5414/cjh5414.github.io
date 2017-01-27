---
layout: post
title:  "이미 push된 file .gitignore 적용하기"
tags:   [Git]
---

> git으로 관리하고 싶지 않은 파일들은 .gitignore 에 명시함으로써 해당 파일을 무시할 수 있다. 하지만 종종 무시할 파일을 .gitignore 에 추가하기 전에 git push 해버리는 경우가 있다. 이 때 뒤늦게 .gitignore 을 수정하여 push를 하지만 원격 저장소에서 해당 파일은 사라지지 않는다.  

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

[좋은 git 사용 습관](https://cjh5414.github.io/git-habit/)  
git commit 과정에 대한 이해와 좋은 commit 습관에 대한 포스트이다.  
