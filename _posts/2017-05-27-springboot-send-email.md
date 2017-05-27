---
layout: post
title:  SpringBoot Gmail SMTP를 이용한 메일 보내기
tags:   ['Java', 'Spring']
---

> SpringBoot에서 메일 보내는 기능을 구현하기 위해 'SpringBoot SMTP', 'SpringBoot 메일 보내기' 등으로 검색을 해보면 다양하고 복잡한 자료들이 나온다. 이 포스트에서는 SpringBoot 프로젝트에서 JavaMailSender과 Gmail을 이용하여 메일을 보내는 간단한 방법에 대해서 알아본다.  

<br/>  

## 사전준비  

Gmail로 메일을 보내기 위해 Google 계정이 필요하다. 계정이 있으면 로그인한 후에 아래의 링크로 접속하여 `보안 수준이 낮은 앱 허용`을 `사용`으로 바꿔준다. (Gmail SMTP를 이용하기 위해 필요하다.)

<https://www.google.com/settings/security/lesssecureapps>

![access](/images/springboot-email/access.png)

<br/>  

## Dependency  

프로젝트에서 사용하고 있는 환경에 맞게 Dependency를 설정해준다.  

### Gradle  

```java
dependencies {
    // ...
    compile("org.springframework.boot:spring-boot-starter-mail")
}
```

<br/>  

### Maven  

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-mail</artifactId>
    <version>1.4.3.RELEASE</version>
</dependency>
```

<br/>  

## Smtp properties 설정  

gmail SMTP를 사용하기 위해 _application.properties_ 파일을 아래와 같이 설정해준다. gmail 계정정보가 필요하다.  

_application.properties_  

```
spring.mail.host=smtp.gmail.com
spring.mail.port=587
spring.mail.username=[gmail 계정]
spring.mail.password=[gmail 비밀번호]
spring.mail.properties.mail.smtp.auth=true
spring.mail.properties.mail.smtp.starttls.enable=true
```  

<br/>  

## 메일 전송 소스 코드  

이제 `JavaMailSender`와 `SimpleMailMessage`를 사용해서 메일을 전송할 수 있다.  

- `to` : 메일을 받을 주소  
- `subject` : 메일 제목  
- `text` : 메일 내용  



```java
import org.springframework.mail.SimpleMailMessage;
import org.springframework.mail.javamail.JavaMailSender;

@Component
public class EmailServiceImpl implements EmailService {

    @Autowired
    public JavaMailSender emailSender;

    public void sendSimpleMessage(
      String to, String subject, String text) {
        ...
        SimpleMailMessage message = new SimpleMailMessage();
        message.setTo(to);
        message.setSubject(subject);
        message.setText(text);
        emailSender.send(message);
        ...
    }
}
```

<br/>  

### 참고자료  

- <http://www.baeldung.com/spring-email>
