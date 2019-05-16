# Spring 웹 MVC 강의 정리
> [백기선의 Spring 웹 MVC](https://www.inflearn.com/course/%EC%9B%B9-mvc/)를 정리한 자료입니다. 

# 서블릿
* 자바 엔터프라이즈 에디션은 웹 애플리케이션 개발용 스팩과 API 제공
* 요청 당 쓰레드(만들거나, 풀에서 가져옴) 사용
* 그 중에 가장 중요한 클래스중 하나가 HttpServlet

## 서블릿의 장점(CGI 에비해)
* 빠르다
* 플랫폼 독립적
* 보안
* 이식성

## 서블릿 엔진 또는 서블릿 컨테이너(톰캣, 제티, 언더토)
* 세션 관리
* 네트워크 기반 서비스
* MIME 기반 메시지 인코딩 디코딩
* 서블릿 생명주기 관리

## 서블릿 생명주기
* 서블릿 컨테이너가 서블릿 인스턴스의 init() 메서드를 호출하여 초기화 한다.
  * 최초 요청을 받았을 때 한번 초기화 하고 나면 그 다음 요청부터는 이 과정을 생략해도된다.
* 서블릿 초기화 된 다음부터는 클라이언트의 요청을 처리할 수 있다. 각 요청은 별도의 스레드로 처리하고 이 때 서블릿 인스턴스의 service()메서드를 호춣한다.
  * 이 안에서 HTTP 요청을 받고 클라이언트로 보낼 때 HTTP 응답을 만든다
  * service()는 보통 HTTP Method에 따라 doGet(), doPost() 등으로 처리를 위임한다.
* 서블릿 컨테이너의 판단에 따라 해당 서블릿 메모리에서 내려와 할 시점에 destroy()를 호출한다.

## 서블릿 리스너와 서블릿 필터

### 서블릿 리스너
* 웹 애플리케이션에서 발생하는 주요 이벤트를 감지하고 각 이벤트에 특별한 작업이 필요한 경우에 사용할 수 있다.
  * 서블릿 컨텍스의 수준의 이벤트
    * 컨텍스트 라이프사이클 이벤트
    * 컨텍스트 애트리뷰트 변경 이벤트
  * 세션 수준의 이벤트
    * 세션 라이프사이클 이벤트
    * 세션 에트리뷰트 변경 이벤트
 
### 서블릿 필터
* 들어온 요청을 서블릿으로 보내고, 또 서블릿이 작성한 응답을 클라이언트로 보내기 전에 특별한 처리기가 필요한 경우에 사용 할 수 있다.
* 체인 형태의 구조

# DispatherServlet

## DispatherServlet 동작 원리

### DispatherServlet 초기화
* 아래의 특별한 타입의 빈을 찾거나, 기본 전략에 해당하는 빈을 등록한다.
  * HandlerMapping(핸들러를 찾아주는 인터페이스), HanlderAdpater(핸들러를 실행하는 인터페이스), HanlderExceptionResolver, ViewResolver, ....

### DispatherServlet 동작 순서
1. 요청을 분석한다. (로케일, 테마, 멀티파크 등등)
2. (핸들러 매핑에게 위임하여) 요청을 처리할 핸들러를 찾는다.
3. (등록되어 있는 핸들러 어뎁터중) 해당 핸들러를 실핼 할 수 있는 "핸들러 어뎁터"를 찾는다.
4. 찾아낸 "핸들러 어뎁터"를 사용해서 핸들러의 응답을 처리한다.
5. (부가적으로) 예외가 발생했다면, 예외 처리 핸들러에 요청 처리를 위임한다.
6. 핸들러의 리턴값을 보고 어떻게 처리할지를 판단한다.
   * 뷰 이름에 해당하는 뷰를 찾아 모델 데이터를 렌더링 한다.
   * `@ResponseBody`가 있다면 Convter를 이용해서 응답 본문을 만든다.
7. 최종적으로 응답을 보낸다.


## DispatherServlet 구성 요소

* DispatcherSerlvet의 기본 전략
  * DispachersServlet.propertes 설정을 따라간다.
* MutilpartResolver
  * 파일 업로드 요청 처리에 필요한 인터페이스
  * HttpServletRequest를 MutilpartHttpServletRequest로 변환해주어 요청이 담고 있는 Fild을 꺼낼수 있는 API 제공
* LocaleReslver
  * 클라이언트의 위치 정보를 파악하는 인터페이스
  * 기본 전략은 요청 accept-language를 보고 판단.
* HanderMapping
  * 요청을 처리할 핸들러를 찾는 인터페이스
* HandlerAdapter
  * HandlerMapping이 찾아낸 핸들러를 처리하는 인터페이스
  * 스프링 MVC 확장력의 핵심
* HanderAdapter
  * HanlderMapping이 찾아낸 "핸들러" 처리하는 인터페이스
* ViewResolver
  * 뷰 이름에 해당하는 뷰를 찾아내는 인터페이스
* FlashMapManager
  * FlashMap 인스턴스를 가져오고 저장하는 인터페이스
  * FlashMap은 주로 리다이렉션을 사용할 때 요청 매개변수를 사용하지 않고 데이터를 전달하고 정리할 때 사용한다.
  * redirect:/events


# MVC 설정
* `WebMvcConfigurationSupport.class` 
* `WebMvcAutoConfiguration.class`
* 설정을 기본 설정이다

## 스프링 부트 MVC 설정

* 스프링 부트의 **주관**이 적용된 자동  설정이 동작한다.
  * JSP 보다 Thymeleaf 선호
  * JSON 지원
  * 정적 리소스 지원 (+ 웰컴 페이지, 파비콘 등 지원)
    * resourceHanderMapping을 기본으로 제공
* 스프링 MVC 커스터마이징 
  * application.properties
  * @Configuration + Implements WebMvcConfigurer: 스프링 부트의 스프링 MVC 자동설정 + 추가 설정
  * @Configuration + @EnableWebMvc + Imlements WebMvcConfigurer: 스프링 부트의 스프링 MVC 자동설정 사용하지 않음.


## WebMvcConfigurer 설정

### Formatter


```java
public class PersonFormatter  implements Formatter<Person> {

    @Override
    public Person parse(String name, Locale locale) {
        return new Person(name, name);
    }

    @Override
    public String print(Person person, Locale locale) {
        return person.toString();
    }
}

@Configuration
public class WebConfig implements WebMvcConfigurer {

    @Override
    public void addFormatters(FormatterRegistry registry) {
        registry.addFormatter(new PersonFormatter());
    }
}

@RestController
@RequestMapping("/sample")
public class SampleApi {

    @GetMapping("/{name}")
    public Person sample(@PathVariable("name") Person person) {
        return person;
    }
}
```
* Formatter를 등록하면 `PathVariable` 어노테이션으로 받을 수 있다.


### handlerIntercepter

* preHandle을 통해서 전처리 가능
* postHandler을 통해 후처리 가능
* afterComplection 완전히 끝난 이후


```java
@Configuration
public class WebConfig implements WebMvcConfigurer {

    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(new SimpleInterceptor()).order(0);
        registry.addInterceptor(new SecondeInterceptor()).order(1);
    }
}

public class SimpleInterceptor implements HandlerInterceptor {

    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        System.out.println("pre handler 1");
        return true;
    }

    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        System.out.println("post handler 1");
    }

    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        System.out.println("after completion 1");
    }
}

    /**
     * preHandle 1 -> 요청 전처리
     * preHandle 2 -> 요청 전처리
     * postHandler 2-> 요청 후처리
     * postHandler 1-> 요청 후처리
     * 뷰 랜더링
     * afterCompletion 2 -> 
     * afterCompletion 1 -> 
     */

@GetMapping("/{name}")
public Person sample(@PathVariable("name") Person person) {
    return person;
}
```
* **postHandler, afterCompletion 순서는 preHandle와 반대이다.**
* `new SimpleInterceptor()).order(0)` 우선순위를 설정을 통해서 조절할 수 있다. (낮을 수록 우선순위가 높다. 음수도 가능하다)

boolean preHandle(request, response, handler)
* 핸들러 실행하기 전에 호출 됨
* **핸들러**에 대한 정보를 사용할 수 있기 때문에 서블릿 필터에 비해 보다 세밀한 로직을 구현할 수 있다.
* 리턴값으로 계속 다음 인터셉터 또는 핸들러로 요청,응답을 전달할지(true) 응답 처리가 이곳에서 끝났는지(false) 알린다.

void postHandle(request, response, modelAndView)
* 핸들러 실행이 끝나고 아직 뷰를 랜더링 하기 이전에 호출 됨
* **뷰**에 전달할 추가적이거나 여러 핸들러에 공통적인 모델 정보를 담는데 사용할 수도 있다.
* 이 메소드는 인터셉터 역순으로 호출된다.
* 비동기적인 요청 처리 시에는 호출되지 않는다.

void afterCompletion(request, response, handler, ex)
* 요청 처리가 완전히 끝난 뒤(뷰 랜더링 끝난 뒤)에 호출 됨
* preHandler에서 true를 리턴한 경우에만 호출 됨
* 이 메소드는 인터셉터 역순으로 호출된다.
* 비동기적인 요청 처리 시에는 호출되지 않는다.

vs 서블릿 필터
* 서블릿 보다 구체적인 처리가 가능하다.
* 서블릿은 보다 일반적인 용도의 기능을 구현하는데 사용하는게 좋다. 반대로 스프링에 특화된 기능을 구현해야 할 때는 `handlerIntercepter`으로 처리한다

### ResourceHanlder

이미지, 자바스크립트, CSS, HTML 파일과 같은 정적인 리소스를 처리하는 핸들러 등록하는 방법

* Default Servlet
  * 서블릿 컨테이너가 기본으로 제공하는 서블릿으로 정적인 리소스를 처리할 때 사용한다

* 스프링 MVC 리소스 핸들러 매핑 등록
*  가장 낮은 우선 순위로 등록
   *  우리가 직접만든 핸들러가 우선순위가 더 높아야 하기 때문에 우선순위가 낮아야한다.
   *  핸뜰러 매핑이 "/" 이하 요청을 처리하도록 하고
   *  최정적으로 리스스 핸들러가 처리하도록 한다.

* 리소스 핸들러 설정
  * 어떤 요청 패턴을 지원할 것인가?
  * 어디서 리소스를 찾을 것인가?

### Http Message Converter


* 요청 본문에서 메시지를 읽어들이거나(@RequestBody), 응답 본문에 메시지를 작성할 때(@ResponseBody) 사용한다.

```java
// 이렇게 등록하게 된 경우 스프링 부트에서 지정하는 WebMvcAutoConfiguration 기본 설정에서 등록한 http message converter를 덮어 씌어서 기존에 있는 컨버터를 삭제하고 추가한것으로 덮어 씌어진다. 사용하지 않는 것이 좋아 보인다.
@Override
public void configureMessageConverters(List<HttpMessageConverter<?>> converters) {

}

// 이렇게 등록하면 기존에 있는 메시지 컨버터에 추가만 하는 형식이라서 안전하다.
@Override
public void extendMessageConverters(List<HttpMessageConverter<?>> converters) {

}
```

#### 기본 HTTP 메시지 컨버터
* 바이트 배열 컨버터
* 문자열 컨버터
* Resource 컨버터
* Form 컨버터 (폼 데이터 to/from MultiValueMap<String, String>)
* (JAXB2 컨버터)
* (Jackson2 컨버터)
* (Jackson 컨버터)
* (Gson 컨버터)
* (Atom 컨버터)
* (RSS 컨버터)

#### 설정 방법
* 기본으로 등록해주는 컨버터에 새로운 컨버터 추가하기: extendMessageConverters
* 기본으로 등록해주는 컨버터는 다 무시하고 새로 컨버터 설정하기: configureMessageConverters
* 의존성 추가로 컨버터 등록하기 (가장 일반적이다.)
  * 메이븐 또는 그래들 설정에 의존성을 추가하면 그에 따른 컨버터가 자동으로 등록 된다.
  * WebMvcConfigurationSupport 기본 설정을 따라 간다.
  * HttpMessageConverter로 @Bean을 등록한다(굳이 커스텀 해야 한다면 이게 제일 좋아 보인다.)


### 그밖에 WebMvcConfigurer 설정

* CROS 설정
* 리턴 값 핸들러 설정 : 스프링 MVC가 제공하는 기본 리턴 값 핸들러 이외에 리턴 핸들러를 추가 할 수 있다.
* 아큐먼트 리졸버 설정 : 스프링 MVC가 제공하는 기본 아규먼트 리졸버 이외에 커스텀한 아규먼트 리졸버를 추가하고 싶을 때 설정한다.
* 뷰 리졸버 설정 : 핸들러에서 리턴하는 뷰 에대한 리졸버이다. 타임리프, JSP 등 다양하게 있다.
* Content Negotiation 설정 : 요청 본문 또는 응답 본문을 어떤 (MIME) 타입으로 보내야 하는지 결정하는 전략을 설정한다


### MVC 설정 마무리

> 출처 배기선님 스프링 웹 MVC 공개 자료

@EnableWebMvc
- 애노테이션 기반의 스프링 MVC 설정 간편화
- WebMvcConfigurer가 제공하는 메소드를 구현하여 커스터마이징할 수 있다.

스프링 부트
- 스프링 부트 자동 설정을 통해 다양한 스프링 MVC 기능을 아무런 설정 파일을 만들지 않아도 제공한다.
- WebMvcConfigurer가 제공하는 메소드를 구현하여 커스터마이징할 수 있다.
- @EnableWebMvc를 사용하면 스프링 부트 자동 설정을 사용하지 못한다.

스프링 MVC 설정 방법
- 스프링 부트를 사용하는 경우에는 application.properties 부터 시작.
- WebMvcConfigurer로 시작
- @Bean으로 MVC 구성 요소 직접 등록

# MVC 활용


## 요청 맵핑하기 6부 커스텀 애노테이션
> 출처 : 백기선의 스프링 웹 MVC

@RequestMapping 애노테이션을 메타 애노테이션으로 사용하기
* @GetMapping 같은 커스텀한 애노테이션을 만들 수 있다.

메타(Meta) 애노테이션
* 애노테이션에 사용할 수 있는 애노테이션
* 스프링이 제공하는 대부분의 애노테이션은 메타 애노테이션으로 사용할 수 있다.

조합(Composed) 애노테이션
* 한개 혹은 여러 메타 애노테이션을 조합해서 만든 애노테이션
* 코드를 간결하게 줄일 수 있다.
* 보다 구체적인 의미를 부여할 수 있다.

@Retention
* 해당 애노테이션 정보를 언제까지 유지할 것인가.
* Source: 소스 코드까지만 유지. 즉, 컴파일 하면 해당 애노테이션 정보는 사라진다는 이야기.
* Class: 컴파인 한 .class 파일에도 유지. 즉 런타임 시, 클래스를 메모리로 읽어오면 해당 정보는 사라진다.
* Runtime: 클래스를 메모리에 읽어왔을 때까지 유지! 코드에서 이 정보를 바탕으로 특정 로직을 실행할 수 있다.

@Target
* 해당 애노테이션을 어디에 사용할 수 있는지 결정한다.

@Documented
* 해당 애노테이션을 사용한 코드의 문서에 그 애노테이션에 대한 정보를 표기할지 결정한다.

메타 애노테이션
* https://docs.spring.io/spring/docs/current/spring-framework-reference/core.html#beans-meta-annotations
* https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/core/annotation/AliasFor.html

## 핸들러 메소드 6부 @Validated

> 출처 : 백기선의 스프링 웹 MVC

* 스프링 MVC 핸들러 메소드 아규먼트에 사용할 수 있으며 validation group이라는 힌트를 사용할 수 있다.
* @Valid 애노테이션에는 그룹을 지정할 방법이 없다.
* @Validated는 스프링이 제공하는 애노테이션으로 그룹 클래스를 설정할 수 있다.


## 핸들러 메소드 12부: Flash Attributes
> 출처 : 백기선의 스프링 웹 MVC

주로 리다이렉트시에 데이터를 전달할 때 사용한다.
* 데이터가 URI에 노출되지 않는다.
* 임의의 객체를 저장할 수 있다.
*  보통 HTTP 세션을 사용한다.

리다이렉트 하기 전에 데이터를 HTTP 세션에 저장하고 리다이렉트 요청을 처리 한 다음 그 즉시 제거한다.

RedirectAttributes를 통해 사용할 수 있다.


## 핸들러 메소드 15부: @RequestBody & HttpEntity
> 출처 : 백기선의 스프링 웹 MVC

@RequestBody
* 요청 본문(body)에 들어있는 데이터를 HttpMessageConveter를 통해 변환한 객체로 받아올 수 있다.
* @Valid 또는 @Validated를 사용해서 값을 검증 할 수 있다.
* BindingResult 아규먼트를 사용해 코드로 바인딩 또는 검증 에러를 확인할 수 있다.

HttpMessageConverter
* 스프링 MVC 설정 (WebMvcConfigurer)에서 설정할 수 있다.
* configureMessageConverters: 기본 메시지 컨버터 대체
* extendMessageConverters: 메시지 컨버터에 추가
* 기본 컨버터
  * WebMvcConfigurationSupport.addDefaultHttpMessageConverters

HttpEntity
* @RequestBody와 비슷하지만 추가적으로 요청 헤더 정보를 사용할 수 있다.


## 모델: @ModelAttribute 또 다른 사용법
> 출처 : 백기선의 스프링 웹 MVC

@ModelAttribute의 다른 용법
* @RequestMapping을 사용한 핸들러 메소드의 아규먼트에 사용하기 (이미 살펴 봤습니다.)
* @Controller 또는 @ControllerAdvice (이 애노테이션은 뒤에서 다룹니다.)를 사용한 클래스에서 모델 정보를 초기화 할 때 사용한다.
* @RequestMapping과 같이 사용하면 해당 메소드에서 리턴하는 객체를 모델에 넣어 준다.
RequestToViewNameTranslator

## 예외 처리 핸들러: @ExceptionHandler
> 출처 : 백기선의 스프링 웹 MVC

특정 예외가 발생한 요청을 처리하는 핸들러 정의
* 지원하는 메소드 아규먼트 (해당 예외 객체, 핸들러 객체, ...)
* 지원하는 리턴 값
* REST API의 경우 응답 본문에 에러에 대한 정보를 담아주고, 상태 코드를 설정하려면 ResponseEntity를 주로 사용한다.

참고
* [Spring Document](https://docs.spring.io/spring/docs/current/spring-framework-reference/web.html#mvc-ann-exceptionhandler)


## 전역 컨트롤러: @(Rest)ControllerAdvice
> 출처 : 백기선의 스프링 웹 MVC

예외 처리, 바인딩 설정, 모델 객체를 모든 컨트롤러 전반에 걸쳐 적용하고 싶은 경우에 사용한다.
* @ExceptionHandler
* @InitBinder
* @ModelAttributes

적용할 범위를 지정할 수도 있다.
* 특정 애노테이션을 가지고 있는 컨트롤러에만 적용하기
* 특정 패키지 이하의 컨트롤러에만 적용하기
* 특정 클래스 타입에만 적용하기

참고
* [Spring Document](https://docs.spring.io/spring/docs/current/spring-framework-reference/web.html#mvc-ann-controller-advice)

