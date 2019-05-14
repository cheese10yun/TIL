# 목차
<!-- TOC -->

- [목차](#목차)
- [Lesson 1: A Simple Remember Me Flow](#lesson-1-a-simple-remember-me-flow)
    - [시나리오 1](#시나리오-1)
    - [시나리오 2](#시나리오-2)
    - [정리](#정리)
- [Lesson 2: Remember Me with Cookie](#lesson-2-remember-me-with-cookie)
    - [Spring Security Remember Me 알고리즘](#spring-security-remember-me-알고리즘)
    - [Remember Me 속성](#remember-me-속성)
- [Lesson 3: Remember Me with Persistence](#lesson-3-remember-me-with-persistence)

<!-- /TOC -->

# Lesson 1: A Simple Remember Me Flow
* 메번 사이트에 방분할 때마다 로그인을 해야하는 문제를 해결 하기 위한 방법
* Remember Me Token을 쿠키에 저장

```java
...
http
    .rememberMe().key("lssAppKey");
```
* 스프링 시큐리티에서 `.rememberMe().key("lssAppKey")` 으로 Remember Me를 활성화 시킵니다. 
* `key("lssAppKey")`는 쿠키의 키값을 의미합니다.

```html
<input id="remember" type="checkbox" name="remember-me" value="true" />
```
* 로그인 페이지에 remember-me 체크 박스를 추가합니다.
* name 값을 반드시`name="remember-me"`로 지정해야합니다.

## 시나리오 1
* remember-me 없이 로그인 합니다.
* JSESSIONID 쿠키를 수동으로 제거합니다.
* 페이지를 새로 고침하면 로그인 페이지로 리다이렉트됩니다.

## 시나리오 2
* remember-me 체크를 하고 로그인 합니다.
* JSESSIONID 쿠키를 수동으로 제거합니다.
* 페이지를 새로 고침해도 현재 상태가 유지됩니다.

## 정리
* JSESSIONID 쿠키는 세션이 끝나면 만료됩니다. 따라서 이 세션이 비활동 또는 로그아웃 으로인해 만료되면 이 쿠키도 만료됩니다.
* remember-me 쿠키는 다릅니다. 세션 만료 되어도 만료되지 않습니다.
* remember-me 쿠키는 기본 설정은 2주후에 만료됩니다. 
* JSESSIONID 쿠키를 제거해도 remember-me 쿠키를 유지하기 때문에 로그인 상태를 유지할 수 있습니다.

 
# Lesson 2: Remember Me with Cookie

## Spring Security Remember Me 알고리즘
```
base64(
    username + ":" +
    expirationTime : ":" +
    md5Hex(
        username + ":" +
        expirationTime : ":" +
        password : ":" +
        key
    )
)
```
* 사용자 이름, 만료시간, 암호가 있음
* **따라서 사용자가 비밀번호를 변경하면 이미 발급된 Remember Me 즉시 무효화됨**

## Remember Me 속성
```java
http
    .rememberMe()
    .tokenValiditySeconds(604800) //(1)
    .key("lssAppKey") //(2)
    .useSecureCookie(true) //(3)
    .rememberMeCookieName("sticky-cookie") //(4)
    .rememberMeParameter("remember") //(5)
```
* (1) emember-me 쿠키의 기본 만료 시간을 변경합니다. 기본값은 2 주에서 1 주 (604800 초)입니다.
* (2) Remember Me 쿠키를 검증하는데 사용되는 비밀키입니다. (md5Hex key에서 사용) 
* (3) 이 변경은 쿠키가 보안되지 않은 연결을 위해 더 이상 전송되지 않는다는 것을 이해하는 것이 중요합니다.
* (3) 쿠키가 보안되지 않은 연결시 쿠키를 전송하지 않는 설정입니다. 즉 HTTPS 사용하지 않는다면 쿠키가 존재하지만 무시되고 효과는 없습니다.
* (4) 기본 설정인 remember-me 키값에서 다른 쿠키의 값으로 변경 할수 있습니다.
* (5) 로그인 페이지에서 체크 박스 name 값에 대한 설정을 변경할 수 있습니다. 기본 설정들은 공격대상이 이기 때문에 반드시 커스텀해서 사용하는 것이 좋습니다.

# Lesson 3: Remember Me with Persistence

```sql
create table if not exists persistent_logins (
  username varchar_ignorecase(100) not null,
  series varchar(64) primary key,
  token varchar(64) not null,
  last_used timestamp not null
);
```
* 해당 테이블을 생성합니다.

```java
@Autowired
private DataSource dataSource;
@Bean
public PersistentTokenRepository persistentTokenRepository() {
    JdbcTokenRepositoryImpl jdbcTokenRepository = new JdbcTokenRepositoryImpl();
    jdbcTokenRepository.setDataSource(dataSource);
    return jdbcTokenRepository;
}
```
* `PersistentTokenRepository` 빈을 등록합니다.

```java
.tokenRepository (persistentTokenRepository ())
```
* 위에서 등록한 빈을 `tokenRepository()` 메서드를 통해서 주입해줍니다.
