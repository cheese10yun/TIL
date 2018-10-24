
# 목차
<!-- TOC -->

- [목차](#목차)
- [Lesson 1: Breaking Down the Authentication Flow](#lesson-1-breaking-down-the-authentication-flow)
    - [DelegatingFilterProxy 란 ?](#delegatingfilterproxy-란-)
    - [UsernamePasswordAuthenticationFilter 란 ?](#usernamepasswordauthenticationfilter-란-)
    - [Tomcat에서 UsernamePasswordAuthenticationFilter 까지](#tomcat에서-usernamepasswordauthenticationfilter-까지)
    - [UsernamePasswordAuthenticationFilter에서 AuthenticationManager 까지](#usernamepasswordauthenticationfilter에서-authenticationmanager-까지)
    - [AuthenticationManager 내부](#authenticationmanager-내부)
    - [다시 Filter](#다시-filter)
- [Lesson 3: The Security Context](#lesson-3-the-security-context)
    - [Security Context 용어 정리](#security-context-용어-정리)
    - [@Async 사용시 문제](#async-사용시-문제)
- [Lesson 4: Configure the Filter Chain](#lesson-4-configure-the-filter-chain)

<!-- /TOC -->

# Lesson 1: Breaking Down the Authentication Flow
* 인증 흐름을 분석하고 이해합니다.

## DelegatingFilterProxy 란 ?
DelegatingFilterProxy는 스프링 시큐리티가 모든 애플리케이션 요청을 감싸게 해서 모든 요청에 보안이 적용되게 하는 서블릿필터이다.(스프링 프레임워크에서 제공) 스프링 프레임워크 기반의 웹 애플리케이션에서 서블릿 필터 라이프 사이클과 연계해 스프링 빈 의존성을 서블릿 필터에 바인딩하는데 사용한다.

## UsernamePasswordAuthenticationFilter 란 ?
사용자명과 비밀번호로 이뤄진 폼기반 인증에 사용하는 가상 URL요청을 감시하고 요청이 있으면 사용자의 인증을 진행함


## Tomcat에서 UsernamePasswordAuthenticationFilter 까지
* 먼저 DelegatingFilterProxy를 호출하게됩니다.
* UsernamePasswordAuthenticationFilter가 발생하게 됩니다.

## UsernamePasswordAuthenticationFilter에서 AuthenticationManager 까지
* 여기서는 인증 시도를 통해 앞으로 진행됩니다.
* 사용자 이름과 암호를 요청에서 가져와 토큰으로 래핑됩니다.
* **토큰은 실제 인증 프로세스의 시작입니다.**
* 토큰을 `AuthenticationManager` 에게 위임합니다.

## AuthenticationManager 내부
* 자신이 가지고 있는 인증 AuthenticationProviders 를통해서 반복해서 인증을 시도합니다.

## 다시 Filter
* 인증이 성공하면 `AuthenticationManager`는 인증 인ㅅ턴스를 `UsernamePAsswordAuthenticationFilter`에 반환합니다.
* 필터는 세션 전략을 사용합니다. 즉 세션을 어떻게 구성했느냐에 따라서 방법이 달라집니다.



# Lesson 3: The Security Context
* `Security Context`가 동일한 세션 내의 요청에 대해 설정되고 저장되고 사용되는 방법을 설명

## Security Context 용어 정리

* SecurityContext - 실행중인 스레드에 관련된 기본적인 보안 정보
* SecurityContextHolder - 보안 정보의 저장 메커니즘
    * **SecurityContentHolder ThreadLocal을 사용하여 세부 정보를 저장합니다. 즉 스레드별로 컨텍스트에 저장됩니다.**

## @Async 사용시 문제
*  `@Async`로 작업하는 경우 새 스레드가 더 이상 주 스레드와 동일한 주체에 액세스 할 수 없다는 것입니다. 이것을 해결하기 위해서. 기본 전략을 변경해야합니다.

```
spring.security.strategy = MODE_INHERITABLETHREADLOCAL // env 속성을 통해이 작업을 수행 할 수 있습니다.
```
```java
SecurityContextHolder.setStrategyName ( "MODE_INHERITABLETHREADLOCAL") // 자바코드로 제어 할 수있습니다.
Object principal = SecurityContextHolder.getContext (). getAuthentication (). getPrincipal (); // 테스트하기 위해 현재 사용자에 대한 정보를 얻을 수 있습니다.
```

# Lesson 4: Configure the Filter Chain
* 필터 체인을 구성하여 상위 레벨 구성을 넘어 저급 레벨 작업을 수행하는 방법을 보여주는 것입니다. 

* 인증 요청을 통해 디버깅하여 기본 필터 체인을 사용할 수 있습니다.

```java
@Component
public class LssLoggingFilter extends GenericFilterBean {
    
    @Override
    public void doFilter(SevletRequest request, ServeltRsponse response, FilterChanin chain) throws IOException, ServletException {
        HttpServletRequest httpServletRequest = (HttpServletRequest) servletRequest;
        String url = httpServletRequest.getRequestURL().toString();
        String queryString = 
        Optional.ofNullable(httpServletRequest.getQueryString()).map(value -> "?" + value).orElse("");
        log.info(String.format("applying LssLoggingFilter for URI: %s%s", url, queryString));

        chain.doFilter(request, response);
    }
}
```
* `GenericFilterBean` 상속해서 `doFilter()` 메서드를 재정의합니다.
* 재정의한 `doFilter()`는 간단하게 로깅 하는 예제입니다.

```java
...
http
    .addFilterBefore (lssLoggingFilter, AnonymousAuthenticationFilter.class)
```
* 해당 필터를 `addFilterBefore()` 메서드를 통해서 의존성을 주입해줍니다.
