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

* **8181 에서 Ajax Call을 이용 8080 REST API를 호출 한다면 SOP를 위반해서 호출을 할 수 없다.** 이러한 문제를 해결하기 위해서 CORS 표쥰이 탄생


# 참고
* [HomoEfficio : Cross Origin Resource Sharing - CORS](https://homoefficio.github.io/2015/07/21/Cross-Origin-Resource-Sharing/)
* [백기선의 스프링부트](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81%EB%B6%80%ED%8A%B8/)