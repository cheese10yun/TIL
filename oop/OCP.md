# 클린 코더스 강의 14.1. OCP(Open-Closed Principle)

* [강의 정리](https://www.youtube.com/watch?v=dqa-IdafeIE)

* 확장에는 열려있고, 변경에는 닫혀있게 하기

![](https://i.imgur.com/A29SfzV.png)

* Copy Module을 컴파일도 안하고 Low Loevel Details를 변경할 수있다.

![](https://i.imgur.com/E8hl4vh.png)

* 신용카드를 받고자할때 계속 if문을 처리 해야함... 정말 안좋은 패턴

## POS Example

![](https://i.imgur.com/eRCQp2Q.png)
* OCP 위반
	- 확장을 위해 소스를 수정했다.
* 해결책
	- 확장이 필요한 행위를 Absctracation
* CheckOut **수정없이**, PaymentMethod를 **확장 할 수있다**.
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
