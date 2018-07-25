# Spring 에제로 보는 SOLID 

예제 코드는 [GitHub](https://github.com/cheese10yun/spring-SOLID)에 공개 되어 있습니다.

SOLID 다소 딱딱하고 장황할 설명으로 인해서 이해하지 못한 부분들을 최대한 쉽게 풀어 이야기해 보고 Spring 기반 베이스로 코드를 작성해서 실무에서 사용 가능한 최소한의 수준을 제공하려 노력했습니다. 부족한 저의 경험이 누군가에게 도움이 되기를 기원합니다.

# 목차
* [단일 책임의 원칙: Single Responsibility Principle 작업중 ..]()
* [개방 폐쇄의 원칙: Open Close Principle]()
* [리스코브 치환의 원칙: The Liskov Substitution Principle 작업중...]()
* [인터페이스 분리의 원칙: Interface Segregation Principle 작업중...]()
* [의존성역전의 원칙: Dependency Inversion Principle 작업중...]()

# 개방 폐쇄의 원칙: Open Close Principle

개방 폐쇄의 원칙: Open Close Principle 핵심 키워드는 다음과 같습니다. 해당 키워드를 기반으로 세부적으로 설명하겠습니다. 

* **확장에는 열려 있고, 변경에는 닫혀 있다.**
* **기능을 추가하거나 변경하면서 그것을 사용하는 코드는 수정하지 않는다.**

대표적인 OCP의 핵심 개념입니다. SOLID를 처음 접하는 분들은 다소 어렵게 느껴지는 것들을 최대한 쉽게 풀어보겠습니다.


## 용어 정리
> **확장 이란 ?**
> 
>새로운 타입을 추가함으로써 새로운 기능을 추가할 수 있다. 즉 확장이란 새로운 타입을 추가함으로써 새로운 기능(요구사항)을 구현한다. **확장에는 열려 있다는 것은 새로운 타입(클래스)을 추가함으로써 기능(요구사항)을 확장하는 것이다..**

> **변경 이란 ?**
> 
> 확장이 발생했을 때 상위 레벨이 영향을 미치지 않아야 한다. **확장(새로운 클래스)이 발생했을 때 해당 코드를 호출하는 쪽에서 변경이 발생하지 않다면 변경에 닫혀 있다는 것이다.**

# 요구사항
* 카드 결제 시스템을 만들어야 한다.
* 현재 지원하는 카드는 신한 카드 하나뿐이다.
* 이제 우리 카드 결제가 추가되어 구현해야 한다.
* 앞으로도 카드는 지속해서 추가될 예정이다.


# OCP를 준수하기 위한 실패 과정들
<p align="center">
    <img src="https://i.imgur.com/a21midG.png">
</p>

기존 PamentController에서 ShinhanCardPaymentService를 의존해서 신한 카드 결제를 진행 헸습니다. 이제 우리 은행 카드결제가 추가되었으니 아래 컨트롤러처럼 기존 신한 카드를 건드리지(변경에 닫혀있다) 않고 우리 카드를 추가하는 가장 쉬운 방법은 아래처럼 컨트롤러를 각각 구성하는 방법입니다. 물론 OCP를 위반하는 코드입니다.

## 카드사 마다 API

```java
@RequestMapping(value = "/ocp/anti/payment/shinhan", method = RequestMethod.POST)
public void pay(@RequestBody ShinhanCardDto.PaymentRequest req){
    shinhanCardPaymentService.pay(req);
}

@RequestMapping(value = "/ocp/anti/payment/woori", method = RequestMethod.POST)
public void pay(@RequestBody WooriCardDto.PaymentRequest req){
    wooriCardPaymentService.pay(req);
}
```
해당 구조는 매우 좋지 않습니다. 카드사가 추가될 때 마다 API를 추가적으로 만들어야 합니다. 이는 확장에 좋지 않은 코드 뿐만 아니라 올바른 카드사의 API를 찾기위한 코드가 필요하게 됩니다.


## 공통 Request로 API 통일

```java
public static class PaymentRequest {
    private String cardNumber;
    private String csv;
    private CardType type;
}
// 컨트롤러
@RequestMapping(value = "/ocp/anti/payment", method = RequestMethod.POST)
public void pay(@RequestBody CardPaymentDto.PaymentRequest req){
    if(req.getType() == CardType.SHINHAN){
        shinhanCardPaymentService.pay(req);
    }else if(req.getType() == CardType.WOORI){
        wooriCardPaymentService.pay(req);
    }
    // 그 해당 카드 이외의 타입이 들어오면 예외처리는 어떻게??...
}
```
넘겨받는 RequestBody 값의 통일로 하나의 API에서 신한 카드, 우리 카드 결제를 진행할 수 있게 되었습니다. 예를 들어 신한 카드는 카드 번호를 shinhanCardNumber, 우리 카드는 number로 사용했을 경우 매번 미세하게 다른 값들을 공통 Request로 통일하게 됩니다. 다음과 같은 장점이 같을 수 있습니다.


* 기존 코드는 카드 파트너사의 API Request에 의존 관계를 분리할 수 있습니다.
    * DIP의 '상위 레벨의 정책은 하위 레벨의 상세 합에 의존하면 안 된다.'를 준수하게 됩니다. (해당 개념은 DIP에 자세하게 포스팅하겠습니다.)
* Request 값을 통일 함으로써 추상화의 기초가 됩니다.


위의 코드는 OCP를 준수하지는 않습니다. **위에서 정리한 용어 변경에 위배됩니다.** 

> 확장(새로운 클래스)이 발생했을 때 해당 코드를 호출하는 쪽에서 **변경이 발생하지 않다면** 변경에 닫혀 있다는 것입니다.

우리 카드가 추가되었을 경우 해당 컨트롤러의 코드에 if이 추가되었습니다. 이는 위에서 정리한 변경이 발생하는 경우입니다. 또 한 카드가 지속해서 추가될 때 마다 해당 카드결제를 위한 if 문을 지속해서 추가해야 합니다. 이런 반복적인 if는 해당 리팩토링 대상일 확률이 매우 높습니다.

그 밖에도 추가될 카드의 결제를 담당하는 XXXPaymentService 클래스들이 지속해서 의존성 이루어집니다. 그 결과 PaymentController는 컨트롤러 계층임에도 너무 많은 책임을 갖게 되며, **확장에 어렵고, 변경에 취약한 구조가 됩니다.**
 
# OCP 준수

<p align="center">
    <img src="https://i.imgur.com/TdGYl8n.png">
</p>

```java
public class PaymentController {
    @RequestMapping(value = "/payment", method = RequestMethod.POST)
    public void pay(@RequestBody CardPaymentDto.PaymentRequest req) {
        final CardPaymentService cardPaymentService = cardPaymentFactory.getType(req.getType());
        cardPaymentService.pay(req);
    }
}
public class ShinhanCardPaymentService implements CardPaymentService {
    @Override
    public void pay(CardPaymentDto.PaymentRequest req) {
        final ShinhanCardDto.PaymentRequest paymentRequest = buildPayment(req);
        shinhanCardApi.pay(paymentRequest);
    }
}
public class WooriCardPaymentService implements CardPaymentService {    
    @Override
    public void pay(CardPaymentDto.PaymentRequest req) {
        final WooriCardDto.PaymentRequest paymentRequest = buildPayment(req);
        wooriCardApi.pay(paymentRequest);
    }
}
```

의존관계를 인터페이스를 통해서 역전시키는 것입니다. **새로운 카드 결제가 추가된다고 하더라도 PaymentController 수정 없이 CardPayment를 확장하고 있습니다.**

다시 한번 정리하면 **새로운 카드 결제가 추가(새로운 결제 타입 추가) 되더라도(확장에 열려있음) 카드 결제를 호출하는 코드 쪽에서 변경이(변경에는 닫혀 있음) 변경이 발생하지 않습니다. 물론 cardPaymentFactory 클래스의 의존성 주입 코드가 한번은 추가됩니다.**

즉 우리 카드 결제라는 새로운 기능이 추가되었을 때 이것을 새로운 타입으로 추가해서 런타임시 교체하여 **확장에는 열려 있고**, 또 그 코드를 호출하는 쪽은 인터페이스를 바라보고 있으므로 **변경에는 닫혀 있는** 구조가 됩니다.

이처럼 인터페이스를 두는 것만 해도 클래스 간의 강한 결합 관계를 느슨한 관계로 만들 수 있습니다. 그렇다면 모든 클래스의 결합 관계를 인터페이스를 두고 느슨한 관계로 유지해야 하는 걸까요?

**제 개인적인 생각은 아니라고 생각합니다.** 모든 변경사항을 예상하는 것은 불가능하며 그것을 하는 것 또한 지나치게 비효율적이라고 생각합니다. 위처럼 명확한 요구사항인 지속해서 카드 결제가 추가되어야 한다. 라는 명확한 변경이 예상되는 시점, 경험을 통해서 예측할 수 있는 시점에 적용하는 것이 바람직하다고 생각합니다.

**그렇다면 중요한 것은 캡슐화, 객체의 올바른 책임, 역할을 부여하여 예측 변경 시점에 OCP를 쉽게 적용할 수 있도록 하는 것이 중요하다고 생각합니다.**



# 참고
* [도서 - 개발자가 반드시 정복해야 할 객체 지향과 디자인 패턴](http://www.kyobobook.co.kr/product/detailViewKor.laf?ejkGb=KOR&barcode=9788969090010)
* [Youtube 강의(백명석님)](https://www.youtube.com/user/codetemplate/videos)