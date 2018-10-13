# Spring Security CSRF 설정


## CSRF 란 ?
* 간단한 이론 소개....


## Security 설정

```java
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        //@formatter:off
        http
                .authorizeRequests()
                    .antMatchers("/").permitAll()
                    .anyRequest().denyAll()
                .and()
                .csrf()
                    .csrfTokenRepository(CookieCsrfTokenRepository.withHttpOnlyFalse())
                .and()
        ;
        //@formatter:on
    }
}
```

## Sample Controller 

```java
@RestController
public class SampleController {

    @GetMapping("/")
    public void sampleGet(){
    }

    @PostMapping("/")
    public void samplePost(){
    }
}
```
간단한 컨트롤러입니다. GET, POST 설정으로 CSRF 테스틀 진행하겠습니다.

## CORS 테스트

### [GET] http://localhost:8080 호출 
간단한 Rest Controller 입니다. 만약 `.csrf().csrfTokenRepository(CookieCsrfTokenRepository.withHttpOnlyFalse())` 코드를 주석 하고 [http://localhost:8080](http://localhost:8080) 해당 API를 호출하면 아래와 같이 쿠키 정보를 주지 않습니다.

![non-cookie](/assets/non-cookie.png) 

위의 주석을 다시 해제하고 [http://localhost:8080](http://localhost:8080)를 호출하면 아래와 같이 쿠키 정보를 받을 수 있습니다. 기본적으로 GET 방식은 CSRF Token 정보를 넘기지 않아도 상관없습니다.
![cookie](/assets/cookie.png)


### [POST] http://localhost:8080 호출 
![cookie-post](/assets/cookie-post.png)

쿠키로 받은 값을 Header에 실어서 POST 호출을 시도하면 `Http Status Code 200`을 넘겨 받은 것을 확인 할 수 있습니다.

이때 주의할 점은 Header Key 값은 `X-XSRF-TOKEN` 입니다. 발급받은 쿠키의 Key 값은 `XSRF-TOKEN`으로 차이가 있습니다. 자세한 내용은 `CookieCsrfTokenRepository.class` 에서 살펴보겠습니다.

만약 중간에 토큰 값을 변조하거나 토큰값을 넘기지 않으면 `Http Status Code403 Forbidden`을 넘겨받습니다.

### CookieCsrfTokenRepository

`csrfTokenRepository(CookieCsrfTokenRepository.withHttpOnlyFalse())` CSRF Token Repository에 넘겨준 객체가 CookieCsrfTokenRepository 객체입니다. `withHttpOnlyFalse()` Static Factory 메서드로 해당 객체를 생성하고 있습니다.

<p align="center">
  <img src="/assets/CookieCsrfTokenRepository-factory.png">
</p>

실제 코드는 위와 같습니다. 메서드 이름에서도 표현되듯이 HttpOnly 설정이 false로 되어 있습니다. 이처럼 Static Factory 메서드는 캡슐화 가독성 측면에서 좋은 점이 있다고 생각합니다.

<p align="center">
  <img src="/assets/CSRF-Meber-filed.png">
</p>

`DEFAULT_CSRF_COOKIE_NAME` 변수에 `XSRF-TOKEN` 값을 할당 하고 있습니다. 그래서 쿠키의 KEY 값이 `XSRF-TOKEN` 으로 나가게됩니다. `DEFAULT_CSRF_HEADER_NAME`도 마찬가지입니다. 그래서 Header Key 값을 `X-XSRF-TOKEN`으로 넘겨줬어야 합니다.