# 용어 정리
* Origin 
    * URL :스키마,(HTTP, HTTPS)
    * hostname : localhost, naver.com
    * prot : 8080
* Same-Origin Policy : 같은 Origin에게만 요청을 보낼수 있다.
* Cross-Origin Resouce Sharing  : 서로 다른 Origin 끼리 리소스를 쉐어할 수 있도록 자원

# 발생 이유
HTTP 요청은 기본적으로 Cross-site HTTP Request가 가능하다. <img>, <link> 등등 태그로 다른 도메인의 리소스를 가져올 수 있다.

**하지만 <script></script>의 스크립트에서 생성된 Cross-Stime HTTP Request는 SOP를 적용받기 때문에 Cross-Stie Http Request가 불가능하다**


AJAX가 널리 사용되면서 <script></script>로 둘러싸여 있는 스크립트에서 생성되는 XMLHttpRequest에 대해서도 Cross-Site HTTP Requests가 가능해야 한다는 요구가 늘어나자 W3C에서 CORS라는 이름의 권고안이 나오게 되었다.

# CORS 발생 시나리오
* localhost:8080 REST API 서버 구동
* localhost:8181 REST API 서버 구동

* **8181 에서 Ajax Call을 이용 8080 REST API를 호출 한다면 SOP를 위반해서 호출을 할 수 없다.** 이러한 문제를 해결하기 위해서 CORS 표준이 탄생



## CORS 요청의 종류
**해당 글은 [Cross Origin Resource Sharing - CORS](https://homoefficio.github.io/2015/07/21/Cross-Origin-Resource-Sharing/)의 블로글 가져온 글입니다.**

### Simple Request
아래의 3가지 조건을 모두 만족하면 Simple Request

* GET, HEAD, POST 중의 한 가지 방식을 사용해야 한다.
* POST 방식일 경우 Content-type이 아래 셋 중의 하나여야 한다.
    * application/x-www-form-urlencoded
    * multipart/form-data
    * text/plain
* 커스텀 헤더를 전송하지 말아야 한다.

### Preflight Request
Simple Request 조건에 해당하지 않으면 브라우저는 Preflight Request 방식으로 요청한다.

따라서, Preflight Request는

* GET, HEAD, POST 외의 다른 방식으로도 요청을 보낼 수 있고,
* application/xml 처럼 다른 Content-type으로 요청을 보낼 수도 있으며,
* 커스텀 헤더도 사용할 수 있다.

### Request with Credential
HTTP Cookie와 HTTP Authentication 정보를 인식할 수 있게 해주는 요청

요청 시 `xhr.withCredentials = true`를 지정해서 Credential 요청을 보낼 수 있고,
서버는 Response Header에 반드시 `Access-Control-Allow-Credentials: true`를 포함해야 하고,
`Access-Control-Allow-Origin` 헤더의 값에는 `*`가 오면 안되고 http://foo.origin과 같은 구체적인 도메인이 와야 한다.

### Request without Credential

CORS 요청은 기본적으로 Non-Credential 요청이므로, `xhr.withCredentials = true`를 지정하지 않으면 Non-Credential 요청이다.

## CORS 관련 HTTP Response Headers
* Access-Control-Allow-Origin
* Access-Control-Expose-Headers
* Access-Control-Max-Age
* Access-Control-Allow-Credentials
* Access-Control-Allow-Methods
* Access-Control-Allow-Headers
  
## ORS 관련 HTTP Request Headers
* Origin
* Access-Control-Request-Method
* Access-Control-Request-Headers
* XDomainRequest


* CORS를 쓰면 AJAX로도 Same Origin Policy의 제약을 넘어 다른 도메인의 자원을 사용할 수 있
* CORS를 사용하려면
    * 클라이언트에서 `Access-Control-**` 류의 HTTP Header를 서버에 보내야 하고,
    * 서버도 `Access-Control-**` 류의 HTTP Header를 클라이언트에 회신하게 되어 있어야 한다.


## 교차 출처 자원 공유 정책

<p align = "center">
    <img src = "/assets/cors-.png">
</p>

URL | 결과
----|---
http://spring.com/find/task | 성공
http://spring.com:8080 | 실패(포트 다름)
https://spring.com | 실패(프로토콜 다름)
http://subspring.com | 실패(호스트 다름)

# 참고
* [HomoEfficio : Cross Origin Resource Sharing - CORS](https://homoefficio.github.io/2015/07/21/Cross-Origin-Resource-Sharing/)
* [백기선의 스프링부트](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81%EB%B6%80%ED%8A%B8/)