# 스프링 AOP 개념

AOP는 OOP를 보완하는 수단으로. 흩어진 Aspect를 모듈화 할 수 있는 프로그래밍 기법

![AOP-con](/assets/AOP-con.png)
* [이미지 출처 : 백기선의 스프링프레임워크 핵김 기술](https://www.inflearn.com/course/spring-framework_core/)

## AOP 주요 개념
* Aspject와 Target
    * Aspject는 각각의 모듈
    * 적용이 되는 대상 
* Advice
    * 해야할 일
* Join Point와 Pointcut
    * Join Point : 메서드 실행 전 후, 끼어들수 있는 포인트
        * 생성자 생성될때, 프로퍼티 접근 할 때
        * 스팩에 조금더 가깝다 
    * Pointcut : 어디에 적용 되어야 포인트 

## AOP 구현체
* 자바
    * AspjectJ
    * 스프링 AOP

## AOP 적용 방법
* 컴파일
    * 자바파일을 클래스 파일로 만들때 바이트 코드로 조작해서 생성해냄
* 로드 타임
    * 로드 타임 위빙 방식으로 로딩 될때 JVM 메모리상에서 끼워 넣는 방식
    * 바이트 코드에는 생성이 되지 앟음
* 런타임
    * 스프링이 사용하는 방식
    * 스프링이 실행될 되고 Bean을 만들 때 Proxy 빈을 만들고 이 Proxy Bean이 AOP가 적용됨

# 스프링 AOP: 프록시 기반 AOP

## 스프링 AOP 특징
* **프록시 기반의 AOP 구현체**
* **스프링 빈에만 AOP를 적용할 수 있다.**
* 모든 AOP 기능을 제공하는 것이 목적이 아니라 스프링 IoC와 연동하여 엔터프라이즈 애플리케이션에서 가장 흔한 문제에 대한 해결책을 제공하는 것이 목적

## 프록시 패턴

![proxy-class](/assets/proxy-class.png)

* **접근제어 또는 부가 기능을 추가할 수 있음**
* 클라이언트는 인터페이스 타입으로 `Proxy` 객체를 사용하게 됨
* 프록시는 원래 객체인 `RealSubject`를 참조하고 있음
* `Proxy` 객체가 원래 있던 `RealSubject`를 감싸서 해당 기능을 수행
 