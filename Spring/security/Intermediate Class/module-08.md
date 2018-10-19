## 목차

<!-- TOC -->

- [목차](#목차)
- [Lesson 1: A Custom Authentication Provider](#lesson-1-a-custom-authentication-provider)
- [Lesson 2: Multiple Providers and the Authentication Manager](#lesson-2-multiple-providers-and-the-authentication-manager)
- [Lesson 3: In-Memory, JDBC and Hibernate/JPA User Storage](#lesson-3-in-memory-jdbc-and-hibernatejpa-user-storage)
- [Lesson 4: Tracking Logged-in Users (NEW)](#lesson-4-tracking-logged-in-users-new)

<!-- /TOC -->
## Lesson 1: A Custom Authentication Provider

* 기본적으로 주 인증 공급자는 DaoAuthenticationProvider가 될 것입니다.
* 제 3 자 시스템에 대해 인증하는 자체 공급자를 선보일 것입니다. 이것은 기본 공급자를 대체 할 것입니다.
* provider의 authenticate 메소드는 다음과 같습니다.
    * 인증을 성공하면 Authentication(인증된) 객체가 리턴됩니다.
    * provider 인증 처리를 지원하 않는다면 null을 리턴합니다.
    * provider 인증을 실패하면 `AuthenticationException`이 발생합니다.
    * 파라미터로 전달된 authentication 객체에 대해서 인증처리를 지원하지 않는다면 null 을 리턴한다
* 제 3 자 인증 서비스와의 진정한 통합을 위해 - 더 많은 정보를 가지고 작업 할 수 있습니다. 따라서 실제 발생한 문제를 기반으로 매우 구체적인 예외를 throw해야합니다.

## Lesson 2: Multiple Providers and the Authentication Manager


## Lesson 3: In-Memory, JDBC and Hibernate/JPA User Storage

## Lesson 4: Tracking Logged-in Users (NEW)