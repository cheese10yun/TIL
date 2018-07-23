## Spring Security

## 스프링 시큐리티란 ?

* 스프링 시큐리티는 ACGEGI 보안으로 부터 시작
  - ACGEGI는 강력한 보안 프레임워크의 하나
    - 단점 : 많은 양의 XML 설정  코드가 필요하다.
  - ACGEGI는 스프링 2.0 부터 스프링 시큐리티로 이름이 변경
    - 보안 설정에 필요한 수백줄의 코드를 간소화
* 스프링 3.0에서 스프링 시큐리티는 보안 설정을 한층 더 간소화 시켰음

## 보안이란 ?
Authenication ---------> ### Authorization
인증                 인증후         권한 부여


### 인증 종류
1. Creendtial(자격) 기반 인증 :
  1. 일반적으로 웹에서 사용하는 대부분의 인증방식, 권한을 부여 받기 위해서 1차례의 인증과정을 거친후에 사용자명과 비밀번호를 입력받아 DB에 저장된 비비밀번호와 일치 여부 판단
  2.스프링 시큐리티에서는 아이디를 Principle, 비밀번호를 Credential 이라고 부란다.

2. 인증 방식 (Two-factor authenication) : 한번에 두가지 방식으로 인증을 받는 것
  1. 예를들어 금융, 은행 웹어플리케이션을 이영해서 온라인 거래를 할 때 로그인, 보안 인증서 두방법으로 인증하는 것
3. 물리적 인증 : 지문인식, 홍채인식 등

### 권한부여 분류
* 부여된 권한 : 적절한 절차로 사용자 인증(Authenication)이 되었 다면, 권한을 부여 해야한다.
* 리소스의 권한(Intercept) : 권한이 없는 자들이 원천적으로 리소스에 접근할 수 없도록 막아내는 것, 즉 적절한 권한을 가진자만 해당 자원에 접근할 수 있도록 자원에 접근할 수 있도록 자원의 외부요청을 원천적으러 가로채는 것


### 스프링에서
* 모든 리소스에 권한 확인을 부여하는 것은 비효율적
* DelegateFilterProxy - > 모든 요청을 가로챔

* DisPatcher Servlet : 모든 요청을 가로채는 클래스


DelegateFilterProxy --------> Spring Security -------> Resouce
                 요청        인증 여부판단 (인증요구 or 리소스접근)

### 우선 순위
1. DelegateFilterProxy
2. DisPacherServlet

* DisPacherServlet에서 필터 체인 방식으러 DelegateFilterProxy 먼저 호출된다

## 요청 흐름
1. 클라이언트 A resocure 접근
2. DelegateFilterProxy 에서 인터셉트 후 SpringSecurity 호출
3. SpringSecurity에서 인증 처리 확인
  1. 미확인시 -> DelegateFilterProxy 리턴
  2. 확인시 -> A resocure 접근


## Spring Web Security

* Authorization
  - 권한 부여, 허가 와 같은 의미로 사용됩니다.


## 정리
* 모든 인증은 인증 매니저 (AuthenicationManager)를 총해서 이루어진다. 인증 매니저를 생성하기 위해 인증 매니저 빌더 (AuthenicationManagerBuilder)가 사용된다.
* 인증 매니저를 이용해서 인증(Authenication) 작업이 수행된다.
* 인증 매니저들은 인증/인가를 위한 UserDetailsService를 통해서 필요한 정보들을 가져온다
* UserDetails는 사용자의 정보 궈한의 묶음이다.

## 다양한 인증 방식

외부에서 인증이 필요하다고 판단되는 경우 가장 필요한 존재는 인증에 대한 실제적 처리를 담당하는 인증매니저 입니다. 인증 매니저는 결과적으로 ㅇ니증과 관련된 모든 정보를 UserDetails라는 타입으로 반환하는데 이를 위해서 자신이 어떻게 관련 정보를 처리해야 하는지를 판단할 UserDetailsService라는 존재를 활용하게 됩니다.

만일 개발자가 인증되는 방식을 수정하고 싶다면 UserDetailsService라는 인터페이스를 구현하고, 인증 매니저에 연결시켜줍니다.

결과로 반환되는 UserDetails는 기본적인 사용자 계정과 같은 정보와 더불어 사용자가 어떤 권한들을 가지고 있는지를 Collection 타입으로 가지고 있습니다.

인증 매니저와 인증 정보를 제공하는 UserDetailsService위 관계를 좀더 쉽게 표현하면 다음과 같은 형태가 됩니다. 
