---
layout: post
title:  호스팅한 Ubuntu Server 접근 관리하기 | password 대신 ssh 사용
tags:   ['Linux']
---

> 서버를 호스팅 받으면 보안상의 문제로 비밀번호를 사용하는 것보다 ssh key를 발급 받아서 사용하는 것이 좋다. linux user를 추가하고 비밀번호를 이용한 접근을 막고 ssh key를 통해 접속하는 방법에 대해서 알아본다. [Taewoo's Tech Blog](https://twpower.github.io/) 를 많이 참고했다.  

<br/>  

## linku 사용자 생성  

ssh로 호스팅 받은 서버에 접속하여 새로운 linux user를 추가한다. (서버의 아이피는 편의상 `111.111.111.111`로 가정한다.)  

```
$ ssh root@111.111.111.111
root@111.111.111.111's password:
Welcome to Ubuntu 14.04.5 LTS (GNU/Linux 3.19.0-25-generic x86_64)
...
```   

```
root@localhost:~$ adduser linku
Adding user `linku' ...
Adding new group `linku' (1000) ...
Adding new user `linku' (1000) with group `linku' ...
Creating home directory `/home/linku' ...
Copying files from `/etc/skel' ...
Enter new UNIX password:
Retype new UNIX password:
passwd: password updated successfully
Changing the user information for linku
Enter the new value, or press ENTER for the default
	Full Name []:
	Room Number []:
	Work Phone []:
	Home Phone []:
	Other []:
Is the information correct? [Y/n] Y
```  

```
root@localhost:~$ adduser linku sudo
Adding user `linku' to group `sudo' ...
Adding user linku to group sudo
Done.
```

> v16.04에서는 `adduser linku sudo`을 먼저 실행하면 유저 추가와 sudo 그룹 추가가 한 번에 된다.  

<br/>  

생성한 유저로 접속이 되는지 확인해본다.  

```
$ssh linku@111.111.111.111
linku@111.111.111.111's password:
Welcome to Ubuntu 14.04.3 LTS (GNU/Linux 3.19.0-25-generic x86_64)
...
```  

<br/>  

## root 사용자 접근 제한  

이제 새로 생성한 유저로만 접속할 수 있도록 root 계정으로의 접근을 제한한다.  

```
linku@localhost:~$ sudo vim /etc/ssh/sshd_config
```  

`/PermitRootLogin` 을 입력하면 해당 키워드를 검색하여 위치로 이동한다. `Shift` + `a` 으로 입력을 시작하여 `yes`를 `no`로 바꿔주고 `wq`로 저장후 종료한다.  

![PermitRootLogin](/images/ubuntu-server-access/permitRootLogin.png)  

설정을 저장하기 위해 아래의 명령을 실행시켜준다.

```
linku@localhost:~$ sudo service ssh restart
```

> v16.04 에서는 `linku@localhost:~$ /etc/init.d/ssh restart`

적용이 안된다면 reboot 하자.

```
linku@localhost:~$ sudo reboot
```

적용이 됐는지 확인하기 위해서는 root 계정을 로그인 했을 때 아래와 같이 password로 접근할 수 없다는 메세지가 뜨면 된다.  

```
root@182.162.104.151's password:
Permission denied (publickey,password).
```  

<br/>  

## Mac에서 SSH Key 발급받기  

서버에 password 대신 SSH Key를 이용하여 접근하기 위해 로컬 컴퓨터인 Mac에서 Key를 발급받는 방법에 대해서 알아본다. Key 발급은 편의상 로컬의 `.ssh/` 위치에서 진행한다.   

```
$ cd .ssh/
```

다음은 키를 생성하는 명령인데,  
`-t` 옵션은 어떤 암호화 알고리즘을 사용할 것인지 선택할 수있다. 기본값은 `rsa`이다.   
`-b` 옵션은 key를 만들때 몇 bit의 key를 만들지 결정하는 옵션이며 기본값은 2048이다.

```
$ ssh-keygen -t rsa -b 2048
Generating public/private rsa key pair.
Enter file in which to save the key (/Users/jihun/.ssh/id_rsa): linku
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in linku.
Your public key has been saved in linku.pub.
The key fingerprint is:
SHA256:... jihun@choejihun-ui-MacBook-Pro.local
The key's randomart image is:
+---[RSA 2048]----+
|          oo     |
|      ....       |
+----[SHA256]-----+
```  

`.ssh/` 위치에 아래와 같이 key가 생성된다.  

```
$ ls
...
linku
linku.pub
...
```  

ssh key를 서버로 전송하기 위해서는 `ssh-copy-id`를 이용한다.   

```
$ brew install ssh-copy-id
```  

ssh key 이름과, linux 계정을 인자로 아래와 같이 명령을 실행하면 key를 서버로 보낼 수 있다.  

```
$ ssh-copy-id -i linku linku@111.111.111.111
/usr/local/bin/ssh-copy-id: INFO: Source of key(s) to be installed: "linku.pub"
/usr/local/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
/usr/local/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
linku@111.111.111.111's password:

Number of key(s) added:        1

Now try logging into the machine, with:   "ssh 'linku@111.111.111.111'"
and check to make sure that only the key(s) you wanted were added.
```   

_.ssh_ 에 config 파일을 생성해서 아래와 같이 설정을 해주면 매번 ip를 입력할 필요없이 설정값을 통해 ssh 접속을 편리하게 할 수 있다.  

_.ssh/config_  

```
Host linku
     HostName 111.111.111.111
     User linku
     IdentityFile ~/.ssh/linku
```  

```
$ ssh linku
Welcome to Ubuntu 14.04.3 LTS (GNU/Linux 3.19.0-25-generic x86_64)
...
```

<br/>  

## password을 이용한 방법 SSH 접근 제한  

위에서 root 계정 접근 제한하는 방법과 비슷하게 `/etc/ssh/sshd_config` 파일의 `PasswordAuthentication` 값을 `no` 로 설정해주면 된다.  

```
linku@localhost:~$ sudo vim /etc/ssh/sshd_config
```  

`/PasswordAuthentication` 로 검색하고 `#PasswordAuthentication yes`로 설정된 값을 `no`로 바꿔준 후에 ssh를 restart 하여 적용시킨다.  

```
linku@localhost:~$ sudo service ssh restart
[sudo] password for linku:
ssh stop/waiting
ssh start/running, process 7691
```

password로 접속하여 확인해보면 거부되는 것을 확인할 수 있다.  

```
$ ssh linku@111.111.111.111
Permission denied (publickey).
```  

<br/>  

## 참고자료  

- [호스팅한 Ubuntu 서버에 유저 추가하기 by TWpower](https://twpower.github.io/linux/2017/03/17/31(%ED%98%B8%EC%8A%A4%ED%8C%85%ED%95%9C-Ubuntu-%EC%84%9C%EB%B2%84%EC%97%90-%EC%9C%A0%EC%A0%80-%EC%B6%94%EA%B0%80%ED%95%98%EA%B8%B0).html)  
- [Ubuntu에서 Root 로그인 막기 by TWpower](https://twpower.github.io/linux/2017/03/31/34(Ubuntu%EC%97%90%EC%84%9C-Root-%EB%A1%9C%EA%B7%B8%EC%9D%B8-%EB%A7%89%EA%B8%B0).html)  
- [SSH KEY를 통해 원격 서버에 접속하기 by TWpower](https://twpower.github.io/linux/2017/03/25/33(SSH-KEY%EB%A5%BC-%ED%86%B5%ED%95%B4-%EC%9B%90%EA%B2%A9-%EC%84%9C%EB%B2%84%EC%97%90-%EC%A0%91%EC%86%8D%ED%95%98%EA%B8%B0).html)  
- [Ubuntu에서 ssh key로만 접속 허용하기 by TWpower](https://twpower.github.io/linux/2017/03/31/35(Ubuntu%EC%97%90%EC%84%9C-ssh-key%EB%A1%9C%EB%A7%8C-%EC%A0%91%EC%86%8D-%ED%97%88%EC%9A%A9%ED%95%98%EA%B8%B0).html)  
