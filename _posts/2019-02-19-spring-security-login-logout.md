---
layout: post
title: Spring Security 로그인 및 로그아웃 기능 사용하기. 로그아웃 후 로그인 두번 되는 문제.
tags:   ['Spring', 'Spring Security']
---

> Sprint Boot에서 Spring Security를 사용하여 로그인, 로그아웃 기능을 이용하는 방법을 알아보고 `로그아웃 후에 redirect된 로그인 페이지에서 로그인을 하면 로그인 페이지가 다시 뜨는 문제`에 대한 원인과 해결방법을 알아본다.  

<br/>  

## User Schema  
Spring Security 에서 기본으로 제공하는 User Schema는 다음과 같다.  

![User Schema](/images/spring-security/user-schema.png)  

User와 Authority는 일대다 관계로 맵핑하고 Authority의 Primary Key 는  복합키로 username + authority 이다. 

<br/>   

### User & Authority Model     

Spring Hibernate 를 이용하여 구현한 User 와 Authority Model  

_User_   
```java
...

@Entity(name = "users")
public class User {
    @Id
    @Column(nullable = false, unique = true)
    private String username;
    @Column(nullable = false, length = 200)
    private String password;
    @Column(nullable = false, columnDefinition = "TINYINT(1)")
    @Type(type = "org.hibernate.type.NumericBooleanType")
    private boolean enabled = true;
    @OneToMany(cascade = CascadeType.ALL)
    @JoinColumn(name = "username")
    private List<Authority> authorities = new ArrayList<>();

    @Column
    private String name;

    ...
}
```  

_Authority_
```java
...

@Entity(name = "authorities")
public class Authority {
    public static final String ROLE_USER = "ROLE_USER";
    public static final String ROLE_ADMIN = "ROLE_ADMIN";
    public static final String ROLE_DBA = "ROLE_DBA";

    private @EmbeddedId AuthorityId id;

    public Authority(String username, String role) {
        id = new AuthorityId(username, role);
    }
}

@Embeddable
@AllArgsConstructor
@NoArgsConstructor
class AuthorityId implements Serializable {
    String username;
    @Column
    String authority;
}
```  

<br/>  

## Spring Security 설정 클래스 구현  
WebSecurityConfigurerAdapter을 상속하여 spring security 관련 설정을 작성한 클래스이다.  

```java
...

@EnableWebSecurity
@Configuration
public class SecurityConfiguration extends WebSecurityConfigurerAdapter {
    private final DataSource dataSource;

    @Autowired
    DevistAuthenticationSuccessHandler devistAuthenticationSuccessHandler;

    @Bean
    public BCryptPasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder();
    }

    @Autowired
    public SecurityConfiguration(DataSource dataSource) {
        this.dataSource = dataSource;
    }

    @Autowired
    public void configureGlobal(AuthenticationManagerBuilder auth) throws Exception {
        auth
                .jdbcAuthentication()
                .dataSource(dataSource)
                .usersByUsernameQuery("select username,password, enabled from users where username=?")
                .authoritiesByUsernameQuery("select username, authority from authorities where username=?")
                .passwordEncoder(passwordEncoder())
                .withUser("admin").password(passwordEncoder().encode("1234")).roles("ADMIN")
                .and()
                .withUser("dbadmin").password(passwordEncoder().encode("1234")).roles("ADMIN", "DBA");
    }

    @Override
    protected void configure(HttpSecurity httpSecurity) throws Exception {
        httpSecurity
                .authorizeRequests()
                    .antMatchers("/resources/**", "/webjars/**", "/static/**", "/signup", "/").permitAll()
                    .antMatchers("/h2-console/**").access("hasRole('ADMIN') and hasRole('DBA')")
                    .anyRequest().authenticated()
                    .and()
                .formLogin()
                    .loginPage("/login").permitAll()
                    .and()
                .logout()
                    .logoutSuccessUrl("/login")
                    .and()
                .csrf()
                    .ignoringAntMatchers("/h2-console/**")
                    .and()
                .headers().frameOptions().disable();
    }
}
```   

<br/>  

## 로그인 처리 과정  

요청이 들어오면 filter 에 의해 권한이 필요한 요청인지 아닌지를 판단하여 필요하면 `/login` 페이지를 반환한다.  
여`authorizeRequests().antMatchers()` 를 이용하여 권한이 필요한 URL을 지정한다.  

`formLogin()` 으로 로그인 처리 관련 설정을 하는데 `loginPage()`에 설정한 URL이 로그인에 사용될 URL이다.  
`.successHandler()` 을 지정하면 로그인 후에 처리할 핸들러를 지정할 수 있다.   

기본 설정에 의하여 Redirect 된 로그인 페이지에서 로그인을 하면 로그인 하기 전 요청했던 URL로 redirect 한다. 
처음부터 로그인 페이지로 접속하여 로그인을 요청하면 완료 후에 기본 설정인 `/` 으로 redirect 된다. 

<br/>

## 로그아웃 처리 과정    

### Logout 기본 설정된 URL   

- 로그아웃 성공 후 반환하는 URL(logoutSuccessUrl) : "/login?logout", GET
- 로그아웃 요청시 사용하는 URL(logoutUrl) : "/logout", POST

로그아웃 요청을 하기 위해서는 `/logout` 을 사용하고 로그아웃 후에는 `/login?logout`으로 redirect 된다.

### 로그인 후에 처리되는 SavedRequestAwareAuthenticationSuccessHandler  

SavedRequestAwareAuthenticationSuccessHandler는 기본으로 들어가 있는 login success handler 이다. 

이 클래스는 RequestCache 라는 객체를 가지고 있는데 `/login`으로 redirect 되는 어떤 요청이 들어오면 관련 Request정보를 저장해두고 나중에 사용한다.

이렇게 request의 정보를 저장해두기 때문에 이용하여 우리가 로그인 하지 않은 상태로 `/todo/add` 와 같은 URL에 요청을 하면 `/login`으로 redirect 되고, 로그인을 하면 다시 `/todo/add` 로 돌아가는 기능을 사용할 수 있는 것이다.

<br/>  

## 로그아웃 후 로그인 페이지가 두번 뜨는 현상  

spring security 기본 설정으로 구현했을 때, 로그아웃 후에 로그인을 할 때의 상황을 생각해보자. 로그아웃을 하면 로그인 페이지로 redirect 된다. 이 상태에서 로그인을 하면 로그인이 성공하지만 다시 로그인 페이지가 뜬다. redirect된 URL은 `/login?logout` 으로 표시된다.  

로그아웃 후에 로그인 할 때 다음과 같은 과정이 처리된다. 로그 아웃을 하면 logoutSuccessUrl인 `/login?logout`으로 redirect 한다. 앞에서 설명한 `SavedRequestAwareAuthenticationSuccessHandler`에 의해 SavedRequest에 `/login?logout`을 저장하고 로그인 페이지를 띄운다. 로그인 요청해서 성공하면 저장되어 있던 `/login?logout`으로 redirect를 시도한다. `/login?logout` 은 `/login` 요청에 logout이라는 파라미터를 추가한 것이기 때문에 로그인 페이지가 다시 뜨게 된다.  

정리해보면,
1. 로그아웃 요청 (`/logout`) 및 성공  
2. `/login?logout` 로 Redirect  
3. SavedRequest로 `/login?logout` URL 저장
4. 로그인 페이지 반환
5. 로그인 요청 및 성공
6. 저장되어 있던 SavedRequest의 URL 인 `/login?logout` 으로 Redirect
7. 다시 로그인 페이지 반환

<br/>  

### 해결방법  

logout의 successUrl을 `/login`으로 설정해준다.  

```
@Override
    protected void configure(HttpSecurity httpSecurity) throws Exception {
        httpSecurity
                .authorizeRequests()
                    ...
                .formLogin()
                    ...
                .logout()
                    .logoutSuccessUrl("/login")
                    .and()
                ...
```   

`/login`으로 요청하여 처리하는 로그인은 SavedRequest 를 따로 저장하지 않기 때문에 로그인이 완료되면 `/`으로 Redirect 된다.

<br/>   

## Spring Security logging 보기 활성화

_application.yml_  

```
logging:
  level:
    org.springframework.security: DEBUG
```  
