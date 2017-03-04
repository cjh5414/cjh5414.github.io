---
layout: post
title:  Git remote branch 가져오기
tags:   [Git]
---

> Git을 사용하다보면 원격 저장소에 있는 branch를 로컬 저장소로 가져와야하는 경우가 있다. 협업하고 있는 다른 팀원의 branch를 가져와서 작업을 해야하는 경우 혹은 혼자서 2대의 PC를 사용하고 작업파일을 Git으로 관리하는데 branch를 따서 작업하는 경우 등이 여기에 해당한다. 저장소를 그대로 clone을 하던지, pull을 하면 원격 저장소의 branch도 같이 받아질 것이라 생각했지만 그렇지 않았다. `$ git checkout -t [원격 저장소의 branch 이름]` 명령을 이용하면 원격 저장소의 branch를 가져오는 것과 동일한 기능을 한다.  

<br/>  

현재 원격 저장소에는 여러 개의 branch가 있다. 하지만 원격의 저장소의 모든 내용을 pull 받은 후에 `$ git branch`로 확인해보면 원격 저장소의 branch는 받아지지 않고 기존에 있던 `master` 브랜치 하나만 존재한다.  

![git branch option](/images/git-remote-branch/git branch option.png)  

### 원격 저장소 branch 확인  

원격 저장소의 branch 리스트를 확인하는 방법이 있다. `$ git branch -r` -r 옵션을 주면 원격 저장소의 branch 리스트를 볼 수 있고, `$ git branch -a` -a 옵션을 주면 로컬, 원격 모든 저장소의 branch 리스트를 볼 수 있다.  

<br/>   

### 원격 저장소의 branch 가져오기  

위의 상황에서 만약 원격 저장소의 `feature/create-meeting` branch를 가져오고 싶다면, `$ git checkout -t origin/feature/create-meeting` 처럼 하면 된다.

![git checkout t option](/images/git-remote-branch/git checkout t option.png)  

`-t` 옵션과 `원격 저장소의 branch 이름`을 입력하면 로컬의 동일한 이름의 branch를 생성하면서 해당 branch로 checkout을 한다.

만약 branch 이름을 변경하여 가져오고 싶다면 `$ git checkout -b [생성할 branch 이름] [원격 저장소의 branch 이름]` 처럼 사용하면 된다.  

<br/>  

### 원격 저장소의 branch 참고하기  

어떤 경우에는 수정 내역을 원격 저장소에 push 하지는 않지만 해당 branch를 참고하기 위해 로컬에 받아서 테스트 해보고 싶은 경우도 있다.  

![git checkout no option](/images/git-remote-branch/git checkout no option.png)  

`$ git checkout [원격 저장소의 branch 이름]` 아무런 옵션없이 원격 저장소의 branch를 checkout 하면 'detached HEAD' 상태로 소스를 보고 변경 해볼 수도 있지만 변경사항들은 commit, push 할 수 없으며 다른 branch로 checkout하면 사라진다.  

<br/>  

## 참고자료  

- <https://blog.outsider.ne.kr/641>  
