# Secure a Simple Spring MVC Application

# Spring Security Properties 기반으로 간단 설정
<!-- TOC -->

- [Secure a Simple Spring MVC Application](#secure-a-simple-spring-mvc-application)
- [Spring Security Properties 기반으로 간단 설정](#spring-security-properties-기반으로-간단-설정)
- [Lesson 2: A Basic Security Java Config](#lesson-2-a-basic-security-java-config)
- [Lesson 3: URL Authorization](#lesson-3-url-authorization)
- [Lesson 4: Building a Login Form](#lesson-4-building-a-login-form)
- [Lesson 5: Implementing Logout](#lesson-5-implementing-logout)
- [Lesson 6: Anonymous “Authentication”](#lesson-6-anonymous-authentication)

<!-- /TOC -->


# Lesson 2: A Basic Security Java Config


```yum
security:
  user:
    name: user
    password: pass
  basic:
    authorize-mode: authenticated
    path: /**
```

```java
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {

    @Autowired
    public void configureGlobal(AuthenticationManagerBuilder auth) throws Exception {

        //@formatter:off
        auth
                .inMemoryAuthentication()
                .withUser("user").password("{noop}pass").roles("USER");
        //@formatter:on

    }
}
```
* properties 설정으로 user의 name, password 간단 설정
* SecurityConfig 에서 properties에 등록된 user security 허용 설정
* security 5.0 부터 (정확하진 않음) password encoder 설정이 필요함 간단하게 위회 하는 방법은 앞에 {noop} 추가하면 됨 password 설정 할 경우 `{noop}pass`


# Lesson 3: URL Authorization
```java
@Override
protected void configure(HttpSecurity http) throws Exception {

    //@formatter:off
    http
        .authorizeRequests()
            .anyRequest().authenticated()
                .antMatchers("/delete/**").hasRole("ADMIN")
                .antMatchers("/delete/**").hasAuthority("ADMIN")
                .antMatchers("/delete/**").hasAnyAuthority("ADMIN", "MANAGER")
                .and()
            .formLogin()
                .and()
            .httpBasic();
    //@formatter:on
}
```
* URL 접근 보안은 `authorizeRequests()` 부터 시작되며 여러 가지 `matchers()`로 매치 규칙을 지정합니다.
* hasRole 메서드는 앞에 ROLE_ 으로 시작 해야 true 리턴 위처럼 하면 안되고 ROLE_ADMIN으로 설정 해야함
* hasAuthority 메서드는 ROLE 으로 시작 안해도됨
* hasAnyAuthority 복수개로 연결 가능 hasAnyRole 메서드도 가능
* UserDetailsBuilder roles(String... roles) 메서드에서 자동으로 ROLE_를 붙이기 때문에 크게 상관 없음

# Lesson 4: Building a Login Form

```java
@Override
protected void configure(HttpSecurity http) throws Exception {
    ...
    http
        .formLogin()
            .loginPage("/login").permitAll()
            .loginProcessingUrl("/doLogin")
}
```
* loginPage 는 아래의 컨트로럴로 매핑 시킨다.
* loginPage.permitAll() 로그인 페이지에대한 접근 권한 설정
* loginProcessingUrl() 메서드는 로그인 페이지 form에 action 입력할 주소 지정
    * form에 action 값과 일치하지 않을 경우에는


```java
public class Controller {
    @RequestMapping("/login")
    public String test(){
        return "loginPage";
    }
}
```
* login view 페이지로 이동 시킴


```html
<form th:action="@{/doLogin}" method="post" class="form-horizontal">
...
</form>
```
* action 위에서 지정했던 loginProcessingUrl 값인 doLogin 입력

# Lesson 5: Implementing Logout

```html
<a th:href="@{/doLogout}" class="menu-right"> Logout </a>
```


```java
protected void configure(HttpSecurity http) throws Exception {
    ...
    http
        //.logoutUrl ( "/ doLogout") 이렇게 지정할 수도 있지만 아래 처럼 지정하는것이 바람직함
        .logout().permitAll()
            .logoutRequestMatcher(new AntPathRequestMatcher("/doLogout", "GET"))

}
```
* 아래 HTML 처럼 @{/doLogout} 지정
* CSRF가 활성화되면 GET은 로그 아웃에 작덩하지 않고 POST만 동작 시켜야 보안상 좋음

다음과 같이 더 디테일 하게 설정 할 수있음
* 로그아웃할 시 인증이 클리어 : clearAuthentication
* 로그아웃시 삭제할 특정 쿠키 지정 : deleteCookies
* 로구아웃시 https session이 무효화되면 벼경: invalidateHttpSession
* 로그아웃성공시 URL 이동: logoutSuccessUrl
* 로구아웃 처리 헨들러 지정 logout success handler


# Lesson 6: Anonymous “Authentication”

* 스프링에서 인증되지 않은 사용자는 모두 Anonymous role을 가짐
* 그 결과 수동으로 null 체크를 구현하고 수동으로이 시나리오를 처리하는 대신 "익명의"사용자가 다시 폴링하는 것이 훨씬 쉽습니다.

```json
{
  "authorities":[
    {
      "authority":"ROLE_ANONYMOUS"
    }
  ],
  "details":{
    "remoteAddress":"0:0:0:0:0:0:0:1",
    "sessionId":null
  },
  "authenticated":true,
  "principal":"anonymousUser",
  "keyHash":1467428350,
  "credentials":"",
  "name":"anonymousUser"
}
```
* ANONYMOUS 의 세션 정보
* authenticated 값이 ture 이다.