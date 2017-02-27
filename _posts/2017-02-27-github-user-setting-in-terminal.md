---
layout: post
title:  Github commit의 사용자 정보가 이름만 보여지는 현상
tags:   [Git]
---

> iMac을 몇 개월 정도 사용할 기회가 생겨서 Git을 새로 설치했다. 기존의 작업하던 내용을 Github에서 clone 받아서 작업한 후 push 했더니 commit의 사용자 정보가 이상하게 나오는 현상이 발생했다. Git을 새로 설치하면 push 하는 과정에서 user 정보를 입력하는데 email 설정이 잘못되어서 발생한 문제같다.  

<br/>

## Git 설치 후 첫 push  

git을 새로 설치한 후에 처음 push를 하면 아래와 같이 git 사용자 정보(username, password)를 입력받는다.  

![git push](/images/github-user-setting/git push.png)   

git push가 성공적으로 완료된다.
하지만 Github repository에 가서 커밋한 내역을 확인해보면 사용자의 정보가 이상하게 나오는 것을 볼 수 있다. 기존의 사용자 정보와 다르게, 이미지가 표시되지 않고 아이디 대신 이름으로 표시된다.  

![wrong user](/images/github-user-setting/wrong user.png)   

<br/>  

## 해결방법  

터미널에서 아래와 같이 git user 정보를 설정해준 후에 다시 `git push`를 하면 해결된다.  

```
$ git config --global user.name "John Doe"
$ git config --global user.email johndoe@example.com
```  

![correct user](/images/github-user-setting/correct user.png)   
