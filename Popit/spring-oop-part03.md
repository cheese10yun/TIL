# Spring OOP 프로그래밍 예제(3) - 추상화, 다형성
예제 코드는 [GitHub](https://github.com/cheese10yun/blog-sample/tree/master/partner-api)에 공개 되어 있습니다.


[Spring OOP 프로그래밍 예제(2) - Value 클래스로 응집력 높이기](https://github.com/cheese10yun/blog-sample/tree/master/bankapi)의 부족했던 부분을 정리한 포스팅입니다.

# 요구사항
* 환율 정보를 조회할 수 있다.
* 환율 정보를 제공해주는 은행은 신한, 우리 은행이 있다.
* 앞으로도 파트너 은행들은 계속 추가된다.
* KRW, VND으로 받는 통화는 신한은행 환율정보를 이용해야 한다
    - USD - > KRW, VND 신한은행 API
* USD으로 받는 통화는 우리은행 환율정보를 이용해야 한다
    - KRW, VND - > USD 우리은행 API

# 요구사항 정리

요구사항을 어렵게 설명했지만 결국은 다형성을 이야기하고 싶었습니다. 이러한 다형성을 지키기 위해서 삽질을 많이 했고 다양한 안티 패턴들을 만났습니다. 왜 이것이 안티 패턴인것인지 설명해 드리고 최종적으로 제가 생각한 것을 공유하려고 합니다. 물론 최종결과물이 높은 수준의 객체지향 코드라고는 생각지 않습니다. 그러나 투박하게나마 객체지향의 다양한 원칙들을 지키려 노력했고 그것을 공유하고 싶습니다.

# 안티패턴

## API 인터페이스

```java
public interface PartnerApi {
    PartnerManagerDto.ExchangeRate get(PartnerManagerDto.ExchangeCurrency dstCurrency);
}
public class ShinhanApi implements PartnerApi {
    @Override
    public PartnerManagerDto.ExchangeRate get(PartnerManagerDto.ExchangeCurrency dstCurrency) {
        return null;
    }
}
public class WooriApi implements PartnerApi {
    @Override
    public PartnerManagerDto.ExchangeRate get(PartnerManagerDto.ExchangeCurrency dstCurrency) {
        return null;
    }
}
```

처음 생각했던 방식입니다. 가장 쉽게 설계할 수 있는 구조로  PartnerApi 인터페이스를 구현 클래스에서 implements를 통한 구현 방식입니다. 하지만 다음과 같은 단점들이 있습니다.

### 객체의 책임이 올바르지 않다
ShinhanApi, WooriApi의 책임은 각각의 은행과 API 통신을 담당하는 것입니다. 즉 각각의 API는 Request, Response 값들은 객체 자신이 자율적인 책임을 가지고 있어야 합니다. **하지만 PartnerApi 인터페이스를 통해서 매게변수와, 리턴 값이 통일 되게 됩니다** 신한, 우리 은행의 환율 정보 API Request, Response 값들은 다를 수밖에 없는 것을 공통된 값으로 변경시키는 것까지 책임이 추가됩니다. 그 결과 SRP, OCP를 위반하게 된다고 생각합니다.

**즉 API 구현 객체의 유일한 변경이유는 파트너사의 API 변경입니다(request, reponse 등등의 변경 ). 그 외에는 변경사항에서는 변경되면 SRP 위반 이라고 저는 생각합니다.**


### API 추가 시 문제
전북은행 API가 추가되었고 계좌 이체할 수 있는 은행 리스트를 가져오는 API가 우리은행, 전북은행에만 추가되었다고 가정했을 경우 아래와 같은 코드가 작성됩니다.

```java
public interface PartnerApi {
    PartnerManagerDto.ExchangeRate getExchangeRate(PartnerManagerDto.ExchangeCurrency dstCurrency);
    PartnerManagerDto.Banks getBanks(PartnerManagerDto.ExchangeCurrency dto);
}

public class JbApi implements  PartnerApi{
    @Override
    public PartnerManagerDto.Banks getBanks(PartnerManagerDto.ExchangeCurrency dto) {
        return 은행 리스트 가져오는 로직;
    }
}

public class WooriApi implements PartnerApi {
    @Override
    public PartnerManagerDto.Banks getBanks(PartnerManagerDto.ExchangeCurrency dto) {
        return 은행 리스트 가져오는 로직;
    }
}

public class ShinhanAPi implements PartnerApi  {
    @Override
    public PartnerManagerDto.Banks getBanks(PartnerManagerDto.ExchangeCurrency dto) {
        // PartnerApi 인터페이스에 getBanks 메소드가 정의되있으니 반드시 구현해야한다.
        // 하지만 신한은행은 은행리스트 조회 기능이 없다.
        return null;
    }
}
```
위처럼 신한 API에서 getBanks를 반드시 구현해야하고 이렇게되면 LSP 위반이 발생합니다.  **우리은행에 getBanks 메서드를 대충 null 정도 리턴하는 코드로 비워 놓지 마세요. 조금만 노력하면 더 좋은 설계가 할 수 있습니다.**


## Manager
```java
public class WooriManager implements PartnerApi {
    @Override
    public PartnerManagerDto.ExchangeRate get(PartnerManagerDto.ExchangeCurrency dstCurrency) {
        return null;
    }
}

public class ShinhanManager implements PartnerApi {
    @Override
    public PartnerManagerDto.ExchangeRate get(PartnerManagerDto.ExchangeCurrency dstCurrency) {
        return null;
    }
}
```

두 번째로 생각했던 방법입니다. 매니저를 두고 해당 매니저가 해당 API를 호출하고 리턴되는 값을 변환작업을 담당하게 했습니다. 그 결과 각각의 구현 API 객체들은 API 통신만 하는 책임만 갖게 되었습니다 매니저의 책임도 분명합니다. 해당 파트너사의 API를 호출하고 공통된 리턴 값으로 돌려준다. 입니다.
**하지만 'API 추가 시 문제'를 해결하지는 못했습니다.** 위 처럼 문제가 똑같이 발생하게 됩니다.

# 최종 결과물
 ```java
 public class ShinhanApi {
     public ShinhanDto.ExchangeRateResponse getExchangeRate(PartnerManagerDto.ExchangeCurrency exchangeCurrency) {
          ...
     return response.getBody();
 }

 public class WooriApi {
     public WooriDto.ExchangeRateResponse getExchangeRate(PartnerManagerDto.ExchangeCurrency exchangeCurrency) {
         ...
     return response.getBody();
 }

 public class ShinhanExchangeRate implements PartnerExchangeRate {
    ...
     @Override
     public PartnerManagerDto.ExchangeRate get(PartnerManagerDto.ExchangeCurrency dto) {
         return PartnerManagerDto.ExchangeRate.builder();
     }
 }

 public class WooriExchangeRate implements PartnerExchangeRate {
     @Override
     public PartnerManagerDto.ExchangeRate get(PartnerManagerDto.ExchangeCurrency dto) {
         return PartnerManagerDto.ExchangeRate.builder();
     }
 }

 public interface PartnerExchangeRate {
     PartnerManagerDto.ExchangeRate get(PartnerManagerDto.ExchangeCurrency dto);
 }
 ```

구현 API 객체는 API 통신만의 책임을 갖게 했습니다. 이것은 매니저에 서도 해결한 문제이니 넘어가겠습니다. 중요한 문제는 ***API 추가 시 문제*** 입니다. 위의 문제들은 한 인터페이스에서 너무 많은 책임을 갖게 되어 추상화가 안 된다는 것으로 생각합니다. 다시 말해 **PartnerApi 인터페이스를 통해서 모든 파트너 API를 규약 시키려 했던 것이 문제라고 생각합니다.**

인터페이스를 PartnerExchangeRate로 두고 환율 정보가 져오는 것에 대해서만 규약 시켰습니다. 그 결과 그 기능이 있는 파트너만 해당 구현체를 갖게 되어 문제를 해결했습니다. 또 ShinhanExchangeRate 객체의 책임은 ShinhanAPi를 통해서 얻은 response 값을 통일감 있는 response 값으로 변경만 시켜주는 책임을 갖습니다.

# 최종 클래스다이어그램
![](https://i.imgur.com/6KxeWmL.png)

# 결론
많은 안티 패턴들을 만났고 그것을 해결했던 과정을 정리하면 저와 같이 고민하는 분들에게 조금이라도 도움이 될 거 같아 부족한 실력임에도 불구하고 정리해 보았습니다. 물론 저 모델도 허점투성이겠죠. 그래도 지금의 제 수준에서는 많은 고민과 시행착오가 있었습니다. 그 덕분에 조금 더 객체지향에 프로그래밍을 더 깊게 공부해 게되 었구요. 또 저런 식으로 작성하게 되면 인터페이스, 클래스가 엄청 많아지는 것에 대해서 조금은 꺼려지게 되는 것도 사실이었습니다. 하지만 아래 첨부된 글을 읽고 더 알맞은 책임으로 더 질게 나누는 것이 더 바람직하다고 생각이 듭니다.



![](http://woowabros.github.io/img/2016-08-03/tray.png)

>**출처 « 로버트 C.마틴 - Clean Code 177page »**
>
>작은 클래스가 많은 시스템이든 큰 클래스가 몇 개뿐인 시스템이든 돌아가는 부품은 그 수가 비슷하다.
 (중략)
 “도구 상자를 어떻게 관리하고 싶은가? 작은 서랍을 많이 두고 기능과 이름을 명확한 컴포넌트를 나눠 넣고 싶은가? 아니면 큰 서랍 몇 개를 두고 모두를 던져 넣고 싶은가?”

[인용 문구, 사진 - 우아한 형제들 기술 블로그 :첫 Java 프로젝트의 생생한 후기](http://woowabros.github.io/experience/2016/08/02/first_java_project.html)

