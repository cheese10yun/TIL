# Spring 에제로 보는 SOLID 

예제 코드는 [GitHub](https://github.com/cheese10yun/spring-SOLID)에 공개 되어 있습니다.

SOLID 다소 딱딱하고 장황할 설명으로 인해서 이해하지 못한 부분들을 최대한 쉽게 풀어 이야기해 보고 Spring 기반 베이스로 코드를 작성해서 실무에서 사용 가능한 최소한의 수준을 제공하려 노력했습니다. 부족한 저의 경험이 누군가에게 도움이 되기를 기원합니다.

# 목차
* [단일 책임의 원칙: Single Responsibility Principle 작업중 ..]()
* [개방 폐쇄의 원칙: Open Close Principle](https://www.popit.kr/spring-%EC%98%88%EC%A0%9C%EB%A1%9C-%EB%B3%B4%EB%8A%94-solid-%EA%B0%9C%EB%B0%A9-%ED%8F%90%EC%87%84%EC%9D%98-%EC%9B%90%EC%B9%99-open-close-principle)
* [리스코브 치환의 원칙: The Liskov Substitution Principle 작업중...]()
* [인터페이스 분리의 원칙: Interface Segregation Principle 작업중...]()
* [의존성역전의 원칙: Dependency Inversion Principle](https://www.popit.kr/spring-%EC%98%88%EC%A0%9C%EB%A1%9C-%EB%B3%B4%EB%8A%94-solid-%EC%9D%98%EC%A1%B4%EC%84%B1%EC%97%AD%EC%A0%84%EC%9D%98-%EC%9B%90%EC%B9%99-dependency-inversion-principle)

# 의존성역전의 원칙: Dependency Inversion Principle
의존성역전의 원칙: Dependency Inversion Principle의 핵심 키워드는 다음과 같습니다. 

상위 수준 정책은 하위 수준 세부 정보에 의존해서는 안됩니다.

* 상위 모듈은 하위 모듈의 구현에 의존해서는 안 된다. 
* 하위의 모듈이 상위 모듈에 정의한 추상 타입에 의존 해야한다.

대표적인 DIP 핵심 키워드 입니다. 단어 하나 하나 플이를 하고 아래의 예제를 통해서 다 다시 이해하는 과정을 통해서 설명드리겠습니다. 다음예제는 아래와 같은 요구사항이 있습니다.

# 요구사항
* 카드 결제 시스템을 만들어야 한다.
* 현재 지원하는 카드는 신한 카드 하나뿐이다.
* 이제 우리 카드 결제가 추가되어 구현해야 한다.
* 앞으로도 카드는 지속해서 추가될 예정이다.

# 용어 풀이

> ***상위 모듈 이란 ?***
>
> 상위 모듈이란 상위 정책을 의미합니다. 위의 요구사항에서는 카드 결제라는 것이 상위 정책을 뜻합니다.

> ***하위 모듈 이란 ?***
>
> 하위 모듈이란 상위 정책에 따른 하위 정책을 말합니다. 위의 요구사항에서 카드 결제라는 상위 정책이 있으면 신한 카드 결제라는 하위(세부) 정책이 있습니다.

> ***추상 타입 이란 ?***
>
> 추상 타입은 인터페이스, 추상화 클래스를 의미합니다. 상위 정책이 하위 정책에 의존하지 않고 추상 타입에 의존하라는 것은 **카드 결제라는 상위 정책이 신한 카드 결제라는 하위 정책을 의존하지 말고 추상 클래스, 인터페이스를 의존하라는 것입니다.**

# DIP 준수하지 않은 코드

<p align="center">
    <img src="https://i.imgur.com/Zkykv9m.png">
</p>



**위 그림은 쉽게 설명해보면 카드 결제(상위 수준 정책)가 신한 카드 결제(하위 수준)에 의존하고 있습니다.** 그렇다면 이것이 갖는 단점이 무엇인지 아래 예제 코드를 통해서 확인해보겠습니다.

## 지나친 의존 관계

```java
class PaymentController {
    @RequestMapping(value = "/dip/anti/payment", method = RequestMethod.POST)
    public void pay(@RequestBody ShinhanCardDto.PaymentRequest req){
        shinhanCardPaymentService.pay(req);
    }   
}
class ShinhanCardPaymentService {
    public void pay(ShinhanCardDto.PaymentRequest req) {
        shinhanCardApi.pay(req);
    }   
}
```
```json
// RequestBody JSON 포멧
{
  "shinhanCardNumber":"4845-9005-9423-4452",  //만약 shinhanCardNumber -> cardNumber 으로 변경된다면 ?
  "cvc":"233"
}
```
카드 결제 기능(상위 정책)이 신한 카드 결제(하위 정책)에 지나치게 의존적입니다. 그 결과 위처럼 신한 카드사의 카드 결제의 JSON의 키값만 변경 시 컨트롤러, 또 그 값을 넘겨주는 프론트엔드 까지 변경까지 영향을 미치게 됩니다. 이렇듯 지나친 의존 관계는 많은 변경 포인트를 유발합니다.

## 확장에 유연하지 못함

```java
@RequestMapping(value = "/anti/payment", method = RequestMethod.POST)
public void pay(@RequestBody CardPaymentDto.PaymentRequest req){
    if(req.getType() == CardType.SHINHAN){
        shinhanCardPaymentService.pay(req);
    }else if(req.getType() == CardType.WOORI){
        wooriCardPaymentService.pay(req);
    }
}
```
우리 카드, 신한 카드 결제 요청을 받을 PaymentRequest Dto 클래스를 생성하였고 CardType으로 해당 맞는 카드 타입에 맞는 서비스를 호출하는 구조입니다. 가장 쉽게 생각할 수 있는 구조이지만 결코 좋은 구조가 아닙니다. 카드가 지속해서 추가될 때마다 해당 카드결제를 위한 if 문을 지속해서 추가해야 합니다. 이런 반복적인 if는 해당 리팩토링 대상일 확률이 매우 높습니다.

그 밖에도 추가될 카드의 결제를 담당하는 XXXPaymentService 클래스들이 지속해서 의존성 이루어집니다. 그 결과 PaymentController는 컨트롤러 계층임에도 너무 많은 책임을 갖게 되며, **확장에 어렵고, 변경에 취약한 구조가 됩니다.** 이 설명은 [개방 폐쇄의 원칙: Open Close Principle](https://github.com/cheese10yun/spring-SOLID/blob/master/docs/OCP.md)에서도 언급했던 내용입니다. 이렇듯 DIP와 OCP는 연관이 크며 SOLID도 각기 다른 메커니즘이 아니라 서로 유기적으로 연결되어 있습니다.



# DIP 준수

<p align="center">
    <img src="https://i.imgur.com/TdGYl8n.png">
</p>

* 상위 모듈은 하위 모듈의 구현에 의존해서는 안 된다. 하위의 모듈이 상위 모듈에 정의한 추상 타입에 의존 해야한다.

DIP의 핵심 내용이 문장장을 위의 요구사항에 맞춰서 풀어 보겠습니다. 

**카드 결제(상위 모듈은)는 신한 카드 결제(하위 모듈)에 의존해서는 안된다. 신한 카드 결제(하위 모듈)은 카드 결제(상위 모듈)에 정의한 카드 결제 인터페이스(추상 타입)에 의존 해야한다.** 

```java
class PaymentController {
    @RequestMapping(value = "/payment", method = RequestMethod.POST)
    public void pay(@RequestBody CardPaymentDto.PaymentRequest req) {
        final CardPaymentService cardPaymentService = cardPaymentFactory.getType(req.getType());
        cardPaymentService.pay(req);
    }
}

public interface CardPaymentService {
    void pay(CardPaymentDto.PaymentRequest req);
}

public class ShinhanCardPaymentService implements CardPaymentService {
    @Override
    public void pay(CardPaymentDto.PaymentRequest req) {
        shinhanCardApi.pay(req);
    }
}
```

이제 코드 레벨에서 다시 정리해 보겠습니다. 의존관계를 인터페이스를 통해서 의존성을 역전 시켰습니다. 컴파일 단계에서는 PaymentController는 PaymentService 인터페이스를 바라보지만 런타임에서는 cardPaymentFactory 통해서 ShinhanCardPaymentService를 바라보게 됩니다. 

또 하위 정책의 신한 카드, 우리 카드가 변경되더라도 PaymenrService 인터페이스를 의존하고 있으므로 확장에 열려 있고 변경에는 닫혀 있는 OCP를 준수하게 됩니다.

# 결론
그렇다면 모든 곳에 대해서 세부 구현체를 바라보는 것이 아니라 인터페이스로 바라보는 것이 올바른 것일까요? **제 개인적인 생각은 그렇지 않다 입니다.**

모든 변경사항에 대응해서 설계한다는 것은 현실적으로 힘듭니다. 위 요구 사항처럼 지속해서 결제 가능한 카드사가 추가된다는 요구사항, 경험을 통해서 예측할 수 있는 상황들에 적용하는 것이 바람직하다고 생각합니다. 

또 개인적인 생각으로는 인프라스트럭처에 대해서는 인터페이스로 바라보는 것이 적절하다고 생각합니다. 인프라스트럭처는 언제든지 대체될 수 있는 영역이기 때문에 이런 영역에 대해서는 인터페이스로 바라보게 설계해서 의존성을 역전 시키는 것이 좋을 거 같습니다.


# 참고
* [도서 - 개발자가 반드시 정복해야 할 객체 지향과 디자인 패턴](http://www.kyobobook.co.kr/product/detailViewKor.laf?ejkGb=KOR&barcode=9788969090010)
* [Youtube 강의(백명석님)](https://www.youtube.com/user/codetemplate/videos)