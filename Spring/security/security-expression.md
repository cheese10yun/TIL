# Spring Security Expressions 정리

> [Intro to Spring Security Expressions](https://www.baeldung.com/spring-security-expressions)를 보고 정리한 글입니다.

# 목차
<!-- TOC -->

- [Spring Security Expressions 정리](#spring-security-expressions-%EC%A0%95%EB%A6%AC)
- [목차](#%EB%AA%A9%EC%B0%A8)
- [Web Security Expressions](#web-security-expressions)
    - [hasRole, hasAnyRole](#hasrole-hasanyrole)
    - [hasAuthority, hasAnyAuthority](#hasauthority-hasanyauthority)
    - [permitAll, denyAll](#permitall-denyall)
    - [isAnonymous, isRememberMe, isAuthenticated, isFullyAuthenticated](#isanonymous-isrememberme-isauthenticated-isfullyauthenticated)
    - [principal, authentication](#principal-authentication)
    - [hasPermission API](#haspermission-api)
- [Expression-Based Access Control](#expression-based-access-control)

<!-- /TOC -->

# Web Security Expressions

Security Expressions는 다음과 같습니다.

* hasRole, hasAnyRole
* hasAuthority, hasAnyAuthority
* permitAll, denyAll
* isAnonymous, isRememberMe, isAuthenticated, isFullyAuthenticated
* principal, authentication
* hasPermission

## hasRole, hasAnyRole

이 표현식은 응용 프로그램의 특정 URL 또는 메소드에 대한 액세스 제어 또는 권한 부여를 정의합니다. 예제를 살펴 보겠습니다.


```java
@Orverride
proteted void configure(final HttpSecurity http) throws Exception{
    ...
    .antMatchers("/auth/admin/*").hasRole("ADMIN")
    .antMatchers("/auth/*").hasAnyRole("ADMIN", "USER")

}
```
## hasAuthority, hasAnyAuthority
약할거ㅏ 권한은 Spring 에서도 비슷합니다. 가장 큰 차이점은 역할에 특별한 의미가 있다는 것입니다. Spring Security 4 부터는 Role 관련 메서드에 의해서 "ROLE_" 가 자동으로 추가됩니다. (ROLE_이 없다면 추가)

그래서 hasAuthority ( 'ROLE_ADMIN') 은 ' ROLE_ '접두사가 자동으로 추가 되기 때문에 hasRole ( 'ADMIN') 과 동일합니다.

**Authority 에서는 ROLE_ 접두사를 사용할 필요가 없습니다.**


```java
@Override
protected void configure(AuthenticationManagerBuilder auth) throws Exception {
    auth.inMemoryAuthenication()
        .withUser("user1").password("password")
        .authroities("USER")
        .and().withUser("admin").password("password")
        .authorities("ADMIN");
}

Override
protected void configure(final HttpSecurity http) throws Exception {
    ...
    .antMatchers("/auth/admin/*").hasAuthority("ADMIN")
    .antMatchers("/auth/*").hasAnyAuthority("ADMIN", "USER")
    ...
}
```

## permitAll, denyAll
서비스 일부 URL에 대한 액새스를 허용하거나 액세스를 거부할 수 있습니다.
```java
...
.antMatchers("/*").permitAll() // 매칭 되는 모든 URL 허용
.antMatchers("/*").denyAll() // 매칭 되는 모든 URL Access 거절
...
```
## isAnonymous, isRememberMe, isAuthenticated, isFullyAuthenticated

사용자의 로그인 상태와 관련된 표현에 중점을 둡니다.

다음을 지정하면 권한이없는 모든 사용자가 기본 페이지에 액세스 할 수 있습니다.
```java
...
.antMatchers("*").anonymous()
...
```
사용하는 모든 사용자가 로그인해야하는 웹 사이트를 보호하려면 isAuthenticated () 메소드 를 사용해야합니다.
```java
...
.antMatchers("*").authenticated();
...
```

sRememberMe () 및 isFullyAuthenticated () 쿠키 사용을 통해 Spring은 remember-me 기능을 사용하므로 매번 시스템에 로그인 할 필요가 없습니다

```java
...
.antMatchers("/*").rememberMe()
...
```

마지막으로, 우리 서비스의 일부에서는 사용자가 이미 로그인되어 있어도 사용자가 다시 인증을 받아야합니다. 예를 들어, 사용자는 설정이나 지불 정보를 변경하려고합니다. 물론 시스템의보다 민감한 영역에서 수동 인증을 요청하는 것이 좋습니다.

그렇게하기 위해서 isFullyAuthenticated ()를 지정할 수 있습니다 .이 함수 는 사용자가 익명 또는 기억하는 사용자가 아닌 경우 true 를 반환 합니다 .

```java
...
.antMatchers("/*").fullyAuthenticated()
...
```

## principal, authentication
이 표현식을 사용 하면 현재 권한이 부여 된 (또는 익명의) 사용자와 SecurityContext 의 현재 Authentication 객체를 나타내는 주요 객체에 각각 액세스 할 수 있습니다.

```java
@RequestMapping(value = "/current-username", method = RequestMethod.GET)
public String currentUsername(Authentication authentication){
    return authentication.getName(); // session에 저장되있는 유저 출력
}
```

## hasPermission API
이 표현식은 표현 시스템과 스프링 시큐리티의 ACL 시스템을 연결하기 위해 [문서화](https://docs.spring.io/spring-security/site/docs/current/reference/html/el-access.html)되어 있으며, 추상 권한을 기반으로 개별 도메인 객체에 대한 권한 제한을 지정할 수 있습니다.

예를 살펴 보겠습니다. 우리는 공동 작문 기사를 주 편집자가 허용하여 다른 저자가 제안한 기사를 출판해야하는지 결정할 수있는 서비스를 제공합니다.

이러한 서비스의 사용을 허용하기 위해 다음과 같은 액세스 제어 방법을 사용할 수 있습니다.
```java
@PreAuthorize("hasPermission(#articleId, 'isEditor')")
public void acceptArticle(Article article) {
   …
}
```

# Expression-Based Access Control

> [Expression-Based Access Control](https://docs.spring.io/spring-security/site/docs/current/reference/html/el-access.html) Document를 보고 간략하게 정리한 글입니다.

Spring Security 3.0은 이전 보았던 구성 속성과 의가 결정권자의 간단한 사용 외에도 권한 부여 메커니즘으로 Spring EL 표현식을 사용 할 수있도록 기능을 도입했습니다.표현식 기반 엑세스 제어는 동일한 아키텍처에서 빌드되지만 복잡한 boolean 로직을 단일 표현식에 캡슐화 할 수 있습니다.


