> 출처 [스프링5.0 마스터](http://www.kyobobook.co.kr/product/detailViewKor.laf?ejkGb=KOR&mallGb=KOR&barcode=9791161751825&orderClick=LAG&Kc=)을 보고 정리한 내용입니다.

# 스프링 부트는 무엇인가?
스프링 부트를 사용하면 "바로 실행"할 수있는 스탠드얼론 프로덕션급 스프링 기반 애를리케이션을 쉽게 만들 수 있다.

> spring-boot-staterparent 의존성은 스프링 부트-기반 애플리케이션에 대한 의존성 및 플러그인 관리를 제공하는 부모 POM이다.

spring-boot-dependencies는 스프링 부트가 사용하는 모든 의존성에 대한 기본 의존성 관리를 제공한다.

의존성의 특정 버전을 덮어쓰려면 애플리케이션의 pom.xml 파이레 적절한 이름을 가진 등록정보를 제공해야한다. 여러 버전의 프레임워크 간 호환성은 개발자가 직면하고 있는 주요 문제중 하나다. 스프링의 특정 버전과 호환되는 최신 스프링 세션 버전은 어떻게 찾을까? 답은 문서를 읽는 것이다. 그러나 스프링 부트를 사용하면 spring-boot-staterparent의 해당 특정 버전의 사용하기 위해 애플리케이션을 업그레이드하면 모든 의존성이 새 스프링 버전과호환되는 버전으로 업그레이드하게 된다.




## 스프링 부트의 오해
* 스프링 부트는 코드 생성 프레임워크가 아니기 때문에 코드를 생성하지 않는다.
* 스프링 부트 애플리케이션 서버, 웹 서버도 아니다. 다양한 애플리케이션 및 웹 서버의 통합을 제공한다.
* 스프링 부트는 특정 프레임워크나 스펙을 구현하지 않는다.

## 스프링 자동 설정

빈 | 설명
--|---
basicErrorController, hanlderExceptionResolver | 기본 예외 처리다. 예외가 발생할 때 기본 에러 페이지를 표시한다.
beanNameHandlerMapping | 핸들러(컨트롤러)에 대한 경로를 확인하는 데 사용하면 된다.
carcterEncodingFilter | 기본 문자 인코딩 UTF-8을 제공한다.
dispacherSerlvet | DispacherServlet은 스프링 MVC 애플리케이션의 프런트 컨트롤러이다.
jacksonObjectMapper | 객체를 XML 또는 JSON으로 또 그 반대로 변환하는 기본 메시지 컨버터다.
mutipartResolver | 웹 애플리케이션의 파일 업로드 지원을 제공한다.
mvcValidator | HTTP 요청의 검증을 지원한다.
viewResolver | 논리적 뷰 이름을 물리적 뷰로 해석한다.
propertySourcesPlaceholderConfigurer | 애플리케이션 구성의 외부화를 지원한다.
requestContextFilter | 요청에 대한 필터를 기본값으로 지정한다.
restTemplateBuilder | REST 서비스를 호출하는 데 사용한다.
tomcatEmbeddedSErvletContaionerFactory | 톰캣은 스프링 부트 기반 웹 애플리케이션을 위한 기본 임베디드 서블렛 컨테이너다.