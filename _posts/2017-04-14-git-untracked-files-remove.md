---
layout: post
title:  git이 추적하지 않는 untracked files 삭제하기
tags:   [Git]
---

> git으로 프로젝트를 관리하다보면 untracked 파일이 쌓이는 경우가 있다. 예를 들어 이미지 업로드 API를 테스트할 때 생성된 이미지 파일 같은 경우 저장소에 넣을 필요가 없다. `.gitignore`에 추가해서 관리할 수도 있지만 이런 경우 임시로 사용된 것이기 떄문에 그럴 필요 까지는 없다. 여태까지는 해당하는 파일들을 직접 지우는 방법으로 git에서 tracking 하지 않도록 해왔었는데 git의 명령어 중에 untracked 파일들을 제거하는 명령어를 이용하면 편리할 것 같아서 찾아보게 되었다.   

<br/>  

```bash
$ git status
On branch master
Untracked files:
  (use "git add <file>..." to include in what will be committed)

	data1.json
	data2.json
	testdir/
```  

git으로 추적되지 않는 파일들이 위와 같이 존재한다.  
untracked 파일들을 모두 지우려면 `$ git clean -f` 명령을 이용하고,

```bash
$ git clean -f
Removing data1.json
Removing data2.json

$ git status
On branch master
Untracked files:
  (use "git add <file>..." to include in what will be committed)

	testdir/

nothing added to commit but untracked files present (use "git add" to track)
```  

testdir/ 과같은 디렉토리 까지 모두 지우고 싶다면 `$ git clean -fd` 명령으로 `-d` 옵션을 추가하면 된다.   

```bash
$ git clean -fd
Removing testdir/

$ git status
On branch master
nothing to commit, working tree clean
```  

<br/>  

### 참고자료  

- <https://blog.outsider.ne.kr/1164>  
