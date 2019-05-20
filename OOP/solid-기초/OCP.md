# 클린 코더스 강의 14.1. OCP(Open-Closed Principle)

* [강의 정리](https://www.youtube.com/watch?v=dqa-IdafeIE)

* 확장에는 열려있고, 변경에는 닫혀있게 하기

![](https://i.imgur.com/A29SfzV.png)

* Copy Module을 컴파일도 안하고 Low Loevel Details를 변경할 수 있다.

![](https://i.imgur.com/E8hl4vh.png)

* 신용카드를 받고자할때 계속 if문을 처리 해야함... 정말 안좋은 패턴

## POS Example

![](https://i.imgur.com/eRCQp2Q.png)
* OCP 위반
	- 확장을 위해 소스를 수정했다.
* 해결책
	- 확장이 필요한 행위를 Absctracation
* CheckOut **수정없이**, PaymentMethod를 **확장 할 수 있다**.
* PaymentMethod를 외부에서 주입받을수 있게 한다

## POS Example

![](https://i.imgur.com/8faZeN0.png)
* 의존성 변화에 주목
	- CheckOut 알고리즘 구현체에 의존하지 않음
	- PaymentMethod의 구현체는 A
* CheckOut 모듈 **수정 없이**, PaymentMethod를 **확장 할 수** 있다.

## Is This Possible ?
* OCP를 준수하면 Modification을 완벽하게 제거 할 수있나 ?
	- 이론족으로 OK
	- But 비실용적

## 나만의 정리

* 새로운 기능으로 변경하려고 기존 코드의 내용을 수정 하는 경우 OCP 위반


### 개방-폐쇄 원칙
* 개방-폐쇄 원칙 OCP은 기존의 코드를 변경하지 않으면서 기능을 추가할 수 있도록 설계 되어야 한다는 뜻이다.
* 확장에는 열려있다.
	- 회원 가입시 발급하는 새로운 쿠폰이 생겼을 경우 쉽게 만들 수 있게 한다.
	- 즉 새로운 클래스를 만들고 슈퍼 클래스를 상속 받아 필요한 부분만 항위에서 재정의한다
* 변경에는 닫혀있다.
	- 기존 코드를 수정 하면 안된다는 뜻이다.
	- 새로운 쿠폰이 발급 되더라도 클래스를 새로 만들어 기존 코드에는 영향을 미치지 않게 한다.
