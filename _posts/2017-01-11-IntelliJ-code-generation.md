---
layout: post
title:  "IntelliJ constructor, getter, setter code generation"
date:   2017-01-09
categories: web
---

> 개발할 때 좋은 도구를 사용하는 것은 생산성을 증가시키기 때문에 중요한 요소라고 생각된다. 개발 도구의 유용한 기능들을 조사하고 익히는 노력들은 가치가 있고 능수능란하게 사용할 수 있게 되면 개발의 재미도 더해준다.  
IntelliJ에서 constructor, getter, setter, toString, equals 등의 코드를 단축키 하나로 생성해주는 편리한 기능이 있다.  

<br/>  

### Customer Class  

Customer class에 대해 constructor, getter, setter, toString 코드를 생성 해본다.  

```java
public class Customer {
    private String id;
    private String name;
    private String email;
}
```  

<br/>  

### Code generation 단축키  

코드를 생성하고자 하는 위치로 커서를 이동한 후  
Mac 에서는 `Command ⌘` + `N`   
Window 에서는 `Ctrl` + `N`  
단축키를 누르면 아래와 같은 메뉴가 나타난다.  

![generator](/images/intellij-code-generator/generator.png)  

원하는 것을 선택하면 Class의 Fields 리스트가 나온다. 코드를 생성하고 싶은 Field 들을 드래그하여 선택한 후 OK 버튼을 누르면 코드가 생성된다.  

### Constructor   

![constructor](/images/intellij-code-generator/constructor.png)  

```java
public Customer(String id, String name, String email) {
    this.id = id;
    this.name = name;
    this.email = email;
}
```  

### Getter & Setter  

![getter setter](/images/intellij-code-generator/getter setter.png)   

```java
public String getId() {
    return id;
}

public void setId(String id) {
    this.id = id;
}

public String getName() {
    return name;
}

public void setName(String name) {
    this.name = name;
}

public String getEmail() {
    return email;
}

public void setEmail(String email) {
    this.email = email;
}
```

### toString  

```java
@Override
public String toString() {
    return "Customer{" +
          "id='" + id + '\'' +
            ", name='" + name + '\'' +
            ", email='" + email + '\'' +
            '}';
}
```
