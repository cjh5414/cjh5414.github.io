---
layout: post
title:  Ubuntu Server에서 Jenkins 이용하기
tags:   ['Linux', 'CI', 'Jenkins']
---

> SpringBoot 프로젝트에 Jenkins를 이용하여 배포, 테스트 자동화 등을 적용하기 위해 Ubuntu 16.04에서 Jenkins를 설치하는 과정을 기록했다.

<br/>  

## Jenkins 설치    

`apt-get`을 이용하여 Jenkins를 설치 하기 위해 저장소 key를 다운받고 (정상적으로 저장소 key를 다운받으면 `OK`를 반환한다.) 패키지 주소를 `sources.list`에 더해준다.   

```
$ wget -q -O - https://pkg.jenkins.io/debian/jenkins-ci.org.key | sudo apt-key add -
OK

$ echo deb http://pkg.jenkins.io/debian-stable binary/ | sudo tee /etc/apt/sources.list.d/jenkins.list
```   

새로 받은 저장소를 적용하기 위해 `apt-get`을 업데이트한 후에 `Jenkins`를 설치한다.  

```
$ sudo apt-get update
$ sudo apt-get install jenkins
```  

<br/>  

## Jenkins 시작하기  

`systemctl`을 이용하여 `Jenkins`를 시작할 수 있다. 하지만 결과를 출력해주지 않기 때문에 `status`를 이용하여 시작이 잘 되었는지 확인해보는 것이 좋다. 잘 동작했다면 아래와 같은 메세지를 출력할 것이다.  

```
$ sudo systemctl start jenkins
$ sudo systemctl status jenkins
● jenkins.service - LSB: Start Jenkins at boot time
  Loaded: loaded (/etc/init.d/jenkins; bad; vendor preset: enabled)
  Active:active (exited) since Thu 2017-04-20 16:51:13 UTC; 2min 7s ago
    Docs: man:systemd-sysv-generator(8)
    ...
```   

<br/>  

## Firewall 허용하기   

`Jenkins`가 잘 동작하면 웹 브라우저를 통해 접근할 수 있도록 방화벽 설정이 필요하다. `Jenkins`는 기본적으로 `8080`포트에서 실행되도록 설정 돼 있다. `ufw`으로 `8080` 포트를 허가해줘서 접근할 수 있도록 하고 결과를 확인해본다.  

```
$ sudo ufw allow 8080
$ sudo ufw status
Status: active

To                         Action      From
--                         ------      ----
OpenSSH                    ALLOW       Anywhere
8080                       ALLOW       Anywhere
OpenSSH (v6)               ALLOW       Anywhere (v6)
8080 (v6)                  ALLOW       Anywhere (v6)
```   

<br/>  

## Jenkins Port 변경  

만약 다른 곳에서 `8080`포트를 사용하고 있으면 중복되어서 `Jenkins`가 동작하지 않을 것이다. 아래와 같이 jenkins 설정 파일에서 포트를 `8081`으로 바꿔줄 수 있다.  

_/etc/default/jenkins_   

```
HTTP_PORT=8081
```    

방화벽 또한 `8081`포트를 허용해주고 `Jenkins`를 재시작하면 된다.  

```
$ sudo ufw allow 8081
$ sudo systemctl restart jenkins
```  

<br/>  

## Jenkins 설정  

이제 웹 브라우저를 통해 Jenkins에 접속할 수 있다.  

`http://ip_address_or_domain_name:8080`    
포트를 변경했다면,  
`http://ip_address_or_domain_name:8081`  
으로 입력한다.  

아래와 같은 화면이 보일텐데 `Jenkins`를 설치할 때 생성된  `/var/lib/jenkins/secrets/initialAdminPassword` 파일의 내용을 입력해야한다.

![unlock](/images/ubuntu-jenkins/unlock.png)  

cat 명령으로 출력한 후 복사해서 붙여넣자.   

```
$ sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```  

완료되면 두 가지 선택사항이 나오는데 `Select plugins to install`을 클릭하면 설치하고 싶은 plugins를 선택할 수 있다. 권장하는 설치항목들을 설치하고 싶다면 `Install suggested plugins`를 선택하자.  

![customize](/images/ubuntu-jenkins/customize.png)    

설치가 완료되면 admin 계정을 생성하고,  

![ready](/images/ubuntu-jenkins/ready.png)  

`Start usgin Jenkins` 을 클릭하면 완료된다.      

![result](/images/ubuntu-jenkins/result.png)  

<br/>  

## 참고자료  

- <https://www.digitalocean.com/community/tutorials/how-to-install-jenkins-on-ubuntu-16-04>  
