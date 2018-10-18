# 스프링 시큐리티

## 용어 정리

### 인증

`인증`은 `주체`의 `신원`을 증명하는 괒어입니다. 주장을 거믕하는 과정입니다. 여기서 주체란 유저, 기기, 시스템 등이 될 수 있지만 보통은 유저(사용자)를 의미합니다. 주체는 자신을 인증해달라고 신원 증며 정보, `크리덴셜`을 제시합니다. 주처게 유저일 경우는 크레딘션은 대게 패스워드 입니다.

### 인가
`인가`(권하부여)는 인증을 마친 유저에게 권한을 부여하여 대상 애플리케이션의 특정 리소스에 접근할 수 있게 허가해주는 과정입니다. **인가는 반드시 인증과정 이후에 수행돼야 하며 권한은 롤 형태로 부여되는 게 일반적입니다.**

### 접근 통제(접근 제어)
애플리케이션 리소스에 접근하는 행위를 제어하는 일입니다. 따라서 어떤 유저가 어떤 리소스에 접근하도록 허락할지 결정하는 행위 즉 `접근 통제 결정`이 뒤 따릅니다. 리소스의 접근 속성과 유저에게 부여된 권한 또는 다른 속성들을 견주어 설정합니다.


스프링은 `WebSecurityConfigurerAdater`라는 구성 어댑터에 준비된 다양한 `configure()`메서드를 이용하면 웹 애플리케이션 보안을 쉽게 구성할 수 있습니다. 

* 폼 기반 로그인 서비스 : 유저가 애플리케이션 로그인하는 기본 폼 페이지를 제공합니다.
* HTTP 기본 인증: 요청 헤더에 표시된 HTTP 기본 인증 크레덴션을 처리합니다. 원격 프로토컬, 웹 서비스를 이용해 인증 요청을 할 때에도 쓰입니다.
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

![security-3](/assets/security-3.png)

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
![security-authentication-filter](/assets/security-authentication-filter.png)

<p align = "center">
  <img src = "/assets/security-authentication-filter.png">
</p>




## 참고
  * [스프링5 레시피](http://www.hanbit.co.kr/store/books/look.php?p_code=B3859466837)
  * [스프링 시큐리티 구조 이해](https://www.slideshare.net/madvirus/ss-36809454)