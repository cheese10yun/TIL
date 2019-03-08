[Youtube](https://www.youtube.com/user/codetemplate/videos)
백명석님의 SOLD 강의 부분 정리

# 목차
- [목차](#%EB%AA%A9%EC%B0%A8)
- [Design Smells (나쁜 디자인에 나타나는 3 가지 냄새)](#design-smells-%EB%82%98%EC%81%9C-%EB%94%94%EC%9E%90%EC%9D%B8%EC%97%90-%EB%82%98%ED%83%80%EB%82%98%EB%8A%94-3-%EA%B0%80%EC%A7%80-%EB%83%84%EC%83%88)
  - [Rigidity](#rigidity)
  - [Fragility](#fragility)
  - [Immobility](#immobility)
  - [What is OOP ?](#what-is-oop)
- [OCP](#ocp)
  - [확장 이란 ?](#%ED%99%95%EC%9E%A5-%EC%9D%B4%EB%9E%80)
  - [변경 이란 ?](#%EB%B3%80%EA%B2%BD-%EC%9D%B4%EB%9E%80)
- [DIP](#dip)

# Design Smells (나쁜 디자인에 나타나는 3 가지 냄새)

## Rigidity
* 정의
  * 시스템의 의존성으로 인해 변경하기 어려워지는 것
* Rigid하게 하는 원인
  * 많은 시간이 소요되는 테스트와 빌드
  * 전체 리빌드를 유발하는 아주 작은 변화
* 테스트와 리빌드 시간을 줄이면
  * Rigidty를 줄이고
  * 수정이 용이해짐

## Fragility
* 한 모듈의 수정이 다른 모듈에 영향을 미칠 때
* 자동차를 SW로 제어
  * 라디오버튼을 수정하는데 자동창문이 영향을 받는 경우
* 해결책
  * 모듈간의 의존성을 제거하는 것

## Immobility
* 모듈이 쉽게 추출 되지 않고 재사용 되지 않은 경우
* 로그인 모듈이 특정 DB에 schema를 사용하고, 특정 UI skin을 사용하지 않은 경우
  

## What is OOP ?
* OOP는 실제 시계를 똑같이 모델링 하는 것
*  Inheritance, Encapsulation, Polymorphism는 OO의 핵심이 아니라 메커니즘
* **OOP의 핵심**
  * IoC를 통해 상위 레벨의 모듈을 하위 레벨의 모듈로 부터 보호하는것
  * Inverted structure tends not to rot
* OOD
  * Dependency Management - Dependency를 잘 관리하는 것이다.
    * Dependency Management를 잘하기 위해서는 SOLID를 해야한다.
  * 상위 수준의 정책과 하위 수준의 세부 정책을 분리하고 그것을 역전 시키는 것이 중요

# OCP

* **확장에는 열려있고 변경에는 닫혀있다**
* **상위 모듈의 수정없이 확장 할 수 있다.**

## 확장 이란 ?
새로운 타입을 추가함으로써 새로운 기능을 추가할 수 있다. 즉 확장이란 새로운 타입을 추가함으로써 새로운 기능(요구사항)을 구현한다.

## 변경 이란 ?
확장이이 발생했을 때 상위 레벨이 영향을 미치지 않아야 한다. OCP를 하기 위해서는 이런것을 하기 위해서 Abstraction, Inversion이 필요하다


# DIP
* **상위 레벨의 정책은 하위 레벨의 상세합에 의존하면 안된다.**
* **상위 레빌의 모듈을 하위 레벌의 부터 보호하는것**
* 이것은 OCP와 연관이 크다.







