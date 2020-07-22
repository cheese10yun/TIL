
## 목차
- [목차](#%EB%AA%A9%EC%B0%A8)
- [용어 정리](#%EC%9A%A9%EC%96%B4-%EC%A0%95%EB%A6%AC)
  - [인증](#%EC%9D%B8%EC%A6%9D)
  - [인가](#%EC%9D%B8%EA%B0%80)
  - [접근 통제(접근 제어)](#%EC%A0%91%EA%B7%BC-%ED%86%B5%EC%A0%9C%EC%A0%91%EA%B7%BC-%EC%A0%9C%EC%96%B4)
- [보안 관련 3요소](#%EB%B3%B4%EC%95%88-%EA%B4%80%EB%A0%A8-3%EC%9A%94%EC%86%8C)
- [스프링 시큐리티와 보안 3요소 의 매칭](#%EC%8A%A4%ED%94%84%EB%A7%81-%EC%8B%9C%ED%81%90%EB%A6%AC%ED%8B%B0%EC%99%80-%EB%B3%B4%EC%95%88-3%EC%9A%94%EC%86%8C-%EC%9D%98-%EB%A7%A4%EC%B9%AD)
- [Authentication과 SecurityContext](#authentication%EA%B3%BC-securitycontext)
  - [Authentication의 주요 메서드](#authentication%EC%9D%98-%EC%A3%BC%EC%9A%94-%EB%A9%94%EC%84%9C%EB%93%9C)
- [AuthenticationManager](#authenticationmanager)
- [(Abstract) SecurityInterceptor](#abstract-securityinterceptor)
- [웹 요청을 FilterChaninProxy 적용](#%EC%9B%B9-%EC%9A%94%EC%B2%AD%EC%9D%84-filterchaninproxy-%EC%A0%81%EC%9A%A9)
- [보안 필터 체인의 주요 구성 요소](#%EB%B3%B4%EC%95%88-%ED%95%84%ED%84%B0-%EC%B2%B4%EC%9D%B8%EC%9D%98-%EC%A3%BC%EC%9A%94-%EA%B5%AC%EC%84%B1-%EC%9A%94%EC%86%8C)
- [Security Authentication](#security-authentication)
  - [AuthenticationManager : AuthenticationProvider 주머니](#authenticationmanager--authenticationprovider-%EC%A3%BC%EB%A8%B8%EB%8B%88)
  - [AuthenticationProvider: 진짜 인증이 일어나는 곳](#authenticationprovider-%EC%A7%84%EC%A7%9C-%EC%9D%B8%EC%A6%9D%EC%9D%B4-%EC%9D%BC%EC%96%B4%EB%82%98%EB%8A%94-%EA%B3%B3)
  - [인증 객체는 ?](#%EC%9D%B8%EC%A6%9D-%EA%B0%9D%EC%B2%B4%EB%8A%94)
  - [결국 우리가 구현해야 할것](#%EA%B2%B0%EA%B5%AD-%EC%9A%B0%EB%A6%AC%EA%B0%80-%EA%B5%AC%ED%98%84%ED%95%B4%EC%95%BC-%ED%95%A0%EA%B2%83)
- [Configure (HttpSecurity http) 정리](#configure-httpsecurity-http-%EC%A0%95%EB%A6%AC)
- [참고](#%EC%B0%B8%EA%B3%A0)

## 용어 정리

### 인증

`인증`은 `주체`의 `신원`을 증명하는 괒어입니다. 주장을 거믕하는 과정입니다. 여기서 주체란 유저, 기기, 시스템 등이 될 수 있지만 보통은 유저(사용자)를 의미합니다. 주체는 자신을 인증해달라고 신원 증며 정보, `크리덴셜`을 제시합니다. 주처게 유저일 경우는 크레딘션은 대게 패스워드 입니다.

### 인가
`인가`(권하부여)는 인증을 마친 유저에게 권한을 부여하여 대상 애플리케이션의 특정 리소스에 접근할 수 있게 허가해주는 과정입니다. **인가는 반드시 인증과정 이후에 수행돼야 하며 권한은 롤 형태로 부여되는 게 일반적입니다.**

### 접근 통제(접근 제어)
애플리케이션 리소스에 접근하는 행위를 제어하는 일입니다. 따라서 어떤 유저가 어떤 리소스에 접근하도록 허락할지 결정하는 행위 즉 `접근 통제 결정`이 뒤 따릅니다. 리소스의 접근 속성과 유저에게 부여된 권한 또는 다른 속성들을 견주어 설정합니다.


스프링은 `WebSecurityConfigurerAdater`라는 구성 어댑터에 준비된 다양한 `configure()`메서드를 이용하면 웹 애플리케이션 보안을 쉽게 구성할 수 있습니다. 

* 폼 기반 로그인 서비스 : 유저가 애플리케이션 로그인하는 기본 폼 페이지를 제공합니다.
* HTTP 기본 인증: 요청 헤더에 표시된 HTTP 기본 인증 크레덴션을 처리합니다. 원격 프로토콜, 웹 서비스를 이용해 인증 요청을 할 때에도 쓰입니다.
* 로그아웃 서비스 : 유저를 로그아웃 시키는 핸들러는 기본 제공합니다.
* 익명 로그인 : 익명 유저도 주체를 할당하고 권한을 부여해서 마치 일반 유저처럼 처리합니다.
* 서블릿 API 연계 : `HttpServletRequest.isUserInRole()`, `HttpServletRequest.getUserPrincipal()` 같은 표준 서블릿 API를 이용해 웹애플리케이션 위치한 보안 정보에 접근합니다.
* CSRF : 사이트 간 요청 위조 방어용 토큰을 생성해 HttpSession에 넣습니다.
* 보안 헤더 : 보안이 적용된 패키지에 대해서 캐시를 해제하는 식으로 XSS 방어, 전송 보안, X-Frame 보안 기능을 제공합니다.

## 보안 관련 3요소

* 접근 주체 (Principal)
  * 보호된 대상에 접근하는 사용자
* 인증 (Authenticate)
  * 현재 사용자가 누군지 확인하는 과정
  * 일반적으로 아이디/암호를 이용해서 인증 처리
* 인가 (Authorize)
  * 현재 사용자가 특정 대상 (URL, 기능 등)을 사용(접근)할 권한이 있는지 검사하는 것

## 스프링 시큐리티와 보안 3요소 의 매칭

<p align = "center">
  <img src = "/assets/security-3.png">
</p>

## Authentication과 SecurityContext
* Authentication 용도
  * 현재 접근 주체 정보를 담는 목적
  * 인증 요청할때, 요청 정보를 담는 목적
* SecurityContext
  * Authentication을 보관
  * 스프링 시큐리티는 현재 사용자에대한 Authentication을 객체를 구할 때 SecurityContext로부터 구함
  * SecurityContext를 기본적으로 쓰레드로컬에 SecurityContext에 보관

### Authentication의 주요 메서드
* String getName(): 사용자의 이름
* Obejct getCredential() : 증명 값(비밀번호 등)
* boolean isAuthenticated() : 인증 여부
* Collection<GrantedAuthority> getAuthroities() : 현재 사용자가 가진 권한

## AuthenticationManager
* 인증을 처리함
* 인증에 성공하면 인증 정로를 담고 있는 Authentication 객체를 리턴
  * 스프링 시큐리티는 리턴한 Authentication 객체를 SecurityContext에 보관 및 인증 상태를 유지하기 위해 세션 보관
* 인증 실패시 AuthenticationException을 발생시킴

## (Abstract) SecurityInterceptor
* 인가 처리를 담당
* 웹의 경우 FilterSecurityInterceptor 구현 사용
* AccessDesicionManager에 권한 검사 위임
* 사용자가 자신의 보안 설정 기준으로 접근 권한이 없는 경우 익셉션 발생

## 웹 요청을 FilterChaninProxy 적용

<p align = "center">
  <img src = "/assets/security-FilterChainProxy.png">
</p>

## 보안 필터 체인의 주요 구성 요소
<p align = "center">
  <img src = "/assets/security-authentication-filter.png">
</p>

## Security Authentication

![](/assets/security-flow.png)

* 필터를 통해서 request가 들어옴
  * Security 가장 앞단에 있는 것은  username password Authentication 필터, CORS 필터 등이 있음
* 필터를 거처 인증 요청 객체러 변환됨
* 인증 요청 객체로 만들어 Provider Manager로 전달됨
  * 이터레이터를 돌면서 클래스에 맞는 Provider 인증처리 진행
  * 인증된 객체 리턴
* isAuthentication의 true가 담겨서 리턴
* SecirtyContext Holder에 LocalThread에 담겨서 필요한 순간에 뿌려줄 수 있게됨


![](/assets/spring-security.png)

### AuthenticationManager : AuthenticationProvider 주머니
* Builder 패턴으로 구현
* 등록된 Authenticaion Provider들에 접근하는 유일한 객체
* 단순한 인터페이스에 불과하다. 내장 구현체: ProviderManager
* AuthenticationManager를 구현해서 쓰지말자. Pivtal 기술자 보다 더 만들 자신 없으면
* **구현해서 쓰라고 넣어준 인터페이스 아니다. 직접 구현하지 말라는 것이다.**

### AuthenticationProvider: 진짜 인증이 일어나는 곳
* `인증전` 객체를 받아 인증 가능 여부를 확인한후, 예외를 던지던 `인증후` 객체를 만들어 돌려준다.
* 구현하라고 넣어준 인터페이스이다.
* 필요에 맞게 정교하게 구현하고 인증 관리자에게 등록시키자.

### 인증 객체는 ?
Authentication 클래스의 모든 서브 클래스

* UsernamePasswordAuthenticationToken 
  * 기본적으로 유저네임과 비밀번호를 받는 방식
  * 인증전 객체이다.
  * 인증이 완료된 객체는 authroities 객체가 담겨있다.

### 결국 우리가 구현해야 할것
* 요청을 받아낼 필터(AbstractAuthenticaionFilter)
* Manager에 등록시킬 Auth Provider
* 인증 정보를 담을 DTO
* 각 인증에 따른 추가 구현체, 기본적인 성공/실패 핸들러.
* 소셜 인증의 경우 각 소셜 공급자 구경에 맞는 DTO와 Http Request
* 인증 시도 / 인증 성송시에 각각 사용할 Authentication 객체



## Configure (HttpSecurity http) 정리

* authorizeRequests 
  *  가장 기본적인 예는 "ROLE_USER"역할이 필요하도록 모든 URL을 구성하는 것입니다. 아래의 구성은 모든 URL에 대한 인증을 요구하며 "admin"및 "user"사용자 모두에게 액세스 권한을 부여합니다.
  *  인증 매커니즘을 요청한 HttpServletRequest 기반으로 설정합니다.
* antMachers() : 요청 패턴을 리스트 형식으로 설정합니다.
* permitAll() : 설정한 리퀘스트 패턴을 누구나 접근할 수 있도록 허용합니다.
* anyRequest() : 설정한 요청 이외의 리퀘스트 요청을 표현합니다.
* authenticated() : 해당 요청은 인증된 사용자만 할 수 있습니다.
* authenticationEntiyPoint(new LoginUrlAuthenticationEntryPoint("/login")) : 인증된 진입 지점입니다. 인증되지 않은 사용자가 허용되지 않은 경로 리퀘쓰트를 오쳥할 경우 `/login` 으로 이동됩니다.




## 참고
  * [스프링5 레시피](http://www.hanbit.co.kr/store/books/look.php?p_code=B3859466837)
  * [스프링 시큐리티 구조 이해](https://www.slideshare.net/madvirus/ss-36809454)
  * [봄이네집 스프링 - (1) Spring Security - Auth0 JWT Library](https://www.youtube.com/watch?v=SMZm2aqI_dQ)