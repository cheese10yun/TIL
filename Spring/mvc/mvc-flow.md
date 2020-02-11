
# Spring MVC 흐름
![spring-mvc-flow](/assets/spring-mvc-flow.png)


* DispacherServlet이 가장 핵심적인 역할을 한다.
* (1) 모든 요청은 DispacherServlet에게 전달된다.
* (2) DispacherServlert이 요청을 받으면 그 요청을 처리할 수 있는 Hanlder의 이름을 HanderMapping 에게 물어본다
* (3) HandlerMapping은 요청 URL을 보고 Hanlder를 판단하고 Handler Name을 과 함께 제어권을 DispacherServlet에게 넘겨준다
* (4) Handler 실행 전에 전처리, 후처리로 실행해야할 인터셉터 목록을 결정한다.
* (5) Service의 비즈니스 로직을 실행한다.
* (6) 랜더링해야하는 View Name을 판단해서 Dispacher Servlet에 전송한다.
* (7) DispacherServlert은 논리적인 View Name을 ViewResolver에 전달한다.
* (8) 전달받은 값으로 적절한 View를 생성한다.
* (9) View에 Model과 컨트롤러를 전달해서
* (10) 응답값을 생성해서 클라이언트에게 반환한다.

## 인터셉터
스프링 MVC 모듈에서 인터셉터를 이용해서 컨트롤러가 요청을 처리하기 전 혹은 후에 대한 로직을 추가할 수 있다. 컨트롤러가 실행되기 전에 인터셉터를 실행할 수 있음으로 주로 특정 URI에 대한 공통 로직 적용이 필요한 경우에 유용하다.