# 목차
<!-- TOC -->

- [목차](#목차)
- [Lesson 1: By URL Authorization with Expressions](#lesson-1-by-url-authorization-with-expressions)
    - [hasIpAddress](#hasipaddress)
    - [negating an expression - not](#negating-an-expression---not)
    - [logical concatenation of expressions](#logical-concatenation-of-expressions)
- [Lesson 2: On-method Authorization with Expressions](#lesson-2-on-method-authorization-with-expressions)
- [Lesson 4: Programmatic Expressions and a custom PermissionEvaluator](#lesson-4-programmatic-expressions-and-a-custom-permissionevaluator)

<!-- /TOC -->
# Lesson 1: By URL Authorization with Expressions

* 보안 페이지에 다양한 유형의 권한을 설정합니다.

## hasIpAddress

```java
.antMatchers("/secured").hasIpAddress("192.168.1.0/24")
```
* 해당 아이피가 아니면 접근하면 403 응답 받게됩니다.

## negating an expression - not
```
.antMatchers("/secured").not().access("hasIpAddress('::1')")
```

## logical concatenation of expressions
```
.antMatchers("/secured").access("hasRole('ROLE_ADMIN') and principal.username == 'user'")
.antMatchers("/secured").access("hasRole('ROLE_ADMIN') or principal.username == 'user'")
```
* 여러 표현식을 연결하여 더욱 강력하고 유연한 표현식을 만들 수 있습니다.
  

# Lesson 2: On-method Authorization with Expressions

```java
@EnableGlobalMethodSecurity(prePostEnabled = true)
```

* 해당 어노테이션 설정

```java
.anyRequest().permitAll()
```

```java
@PreAuthorize("isAuthenticated()") //(1)
@PreAuthorize("hasRole('ADMIN')") // (2)
@PreAuthorize("principal.username=='user'") //(3)
```
* PreAuthorize 사전 검증으로 컨트롤러에 등록하면 햐당 컨트롤러 로직이 실행전에 검사한다.
* (1) 인증된 유저만 허용한다
* (2) role admin인 것만 허용한다.
* (3) principal.username이 'user' 인것만 허용한다.


#  Lesson 4: Programmatic Expressions and a custom PermissionEvaluator

* 접근 Expressions 식을 커스컴 하여 더욱 효율적으로 사용 할 수 있습니다.


```java
@PreAuthorize("isAdmin()") // Expressions 커스컴 해서 사용 할 수있습니다.
public ModelAndView list() {...} 

public class MySecurityExpressionRoot extends SecurityExpressionRoot {
    public MySecurityExpressionRoot(Authentication authentication) {
        super(authentication);
    }
}

public boolean isAdmin() {
    return false;
}
```
* Expressions 커스컴을 하기 위해서는  `SecurityExpressionRoot` 재정의 해야합니다.
* 리턴 타입이 `boolean` 인 메서드를 작성합니다. `isAdmin()` 메서드를 통해서 관리자에 대한 접근 설정을 진행할 수있습니다.

