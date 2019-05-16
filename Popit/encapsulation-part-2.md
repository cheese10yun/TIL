**해당 코드는 [Github](https://github.com/cheese10yun/blog-sample/tree/master/encapsulation#%EB%82%B4%EA%B0%80-%EC%83%9D%EA%B0%81%ED%95%98%EB%8A%94-%EC%BA%A1%EC%8A%90%ED%99%94%EB%9E%80-2)에 공개되어 있습니다.**

객체지향 개념에서 캡슐화는 정말 중요한 개념이라고 생각합니다. 캡슐화를 잘 지켜야 클래스 간의 결합도를 낮추어 코드를 유지 보수하기 쉽게 합니다. 이미 수많은 책이 이 개념에 관해서 설명하고 있습니다. 글을 읽을 때는 이해되지만 정작 캡슐화 좋은 코드를 작성하는 것은 또 다른 영역입니다.

저와 같은 주니어분들이 조금이라도 이해를 돕기 위해 제가 생각하는 캡슐화에 대해서 실무에서 많이 사용하는 Spring Boot, JPA 기반에서 설명해볼까 합니다.


## 요구사항 
* 쿠폰을 통해 할인을 받을 수 있다.
* 쿠폰의 종류는 다양하고 지속해서 추가될 예정이다.
  * 현재는 첫 구매 시 할인해 주는 쿠폰이 있다.



## 묻지 말고 시켜라
> 메시지를 먼저 결졍하고 객체가 메시지를 따르게 하는 설계 방식은 객체가 외부에 제공하는 인터페이스가 독특한 스타일을 따르게 한다. 이 스타일을 묻지 말고 시켜라 **Tell, Don't**

> 송신자는 수신자가 어떤 객체 인지 모르기 때문에 객체에 관해 꼬치꼬치 캐물을 수 없다. 단지 송신자는 수신자가 어떤 객체인지는 모르지만 자신이 전송한 메시지를 잘 처리할 것이라는 것을 믿고 메시지를 전송할 수 밖에 없다.

> 이런 스타일의 협력 패턴은 `묻지 말고 시켜라`라는 이름으로 널리 알려져 있다. 이 스타일은 객체지향 애플리케이션이 자율적인 객체들의 공동 체라는 사실을 강조한다. 어떤 객체가 존재하는지도 모르는데 어떻게 객체의 내부 상태를 가정할 수 있겠는가 ?

> **객체는 다른 객체의 상태를 묻지 말아야 한다. 객체가 다른 객체의 상태를 묻는 다는 것은 메시지를 전송하기 이전에 객체가 가져야 하는 상태에 관해 너무 많이 고민 하고 있다는 증거다.** 고민을 연기하라 단지 필요한 메시지를 전송하기만 하고 메시지를 수신하는 객체가 스스로 메시지의 처리 방법을 결정하게 하라.

* 출처 [객체지향의 사실과 오해](http://www.yes24.co.kr/24/goods/18249021) (너무 좋은 책입니다. 꼭 읽어 보세요)


## 쿠폰 객체
```java
@Entity
@Table(name = "coupon")
@Getter
@NoArgsConstructor(access = AccessLevel.PROTECTED)
public class Coupon {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private long id;

    @Column(name = "used", nullable = false)
    private boolean used;

    @Column(name = "amount", nullable = false, updatable = false)
    private double amount;

    @Column(name = "expiration_date", nullable = false, updatable = false)
    private LocalDate expirationDate;

    @Builder
    public Coupon(double amount, LocalDate expirationDate) {
        this.amount = amount;
        this.expirationDate = expirationDate;
        this.used = false;
    }

    public boolean isExpiration() {
        return LocalDate.now().isAfter(expirationDate);
    }

    public void apply() {
        verifyCouponIsAvailable();
        this.used = true;
    }

    private void verifyCouponIsAvailable() {
        verifyExpiration();
        verifyUsed();
    }

    private void verifyUsed() {
        if (used) {
            throw new IllegalStateException("이미 사용한 쿠폰입니다.");
        }
    }

    private void verifyExpiration() {
        if (LocalDate.now().isAfter(getExpirationDate())) {
            throw new IllegalStateException("사용 기간이 만료된 쿠폰입니다.");
        }
    }
}
```
쿠폰이 사용 가능한지 해당 객체에서 관리하고 있습니다. **이 처럼 해당 객체가 본인의 상태를 결정하고 그 행동까지 실행 가능한지 아닌지를 객체 스스로가 알고 있습니다.**

## 서비스 계층

```java
@Service
@Transactional
public class FirstOrderCoupon implements CouponIssueAble {

    private final CouponRepository couponRepository;

    public FirstOrderCoupon(CouponRepository couponRepository) {
        this.couponRepository = couponRepository;
    }

    @Override
    public boolean canIssued() {
        // TODO: 첫 구매인지 확인 하는 로직 ...
        return true;
    }

    /**
     * 안티 패턴
     *
     * 꼬치꼬치 캐묻고 있습니다.
     */
    public void antiApply(final long couponId) {
        final Coupon coupon = couponRepository.findById(couponId).get();

        if (LocalDate.now().isAfter(coupon.getExpirationDate())) {
            throw new IllegalStateException("사용 기간이 만료된 쿠폰입니다.");
        }

        if (coupon.isUsed()) {
            throw new IllegalStateException("이미 사용한 쿠폰입니다.");
        }

        if (canIssued()) {
           coupon.setUsed(false);
        }
    }

    /**
     * 좋은 패턴
     *
     * 묻지 말고 시켜라. 쿠폰 객체의 apply() 메서드를 통해서 묻지 말고 쿠폰을 적용하고 있습니다.
     */
    public void apply(final long couponId) {
        if (canIssued()) {
            final Coupon coupon = couponRepository.findById(couponId).get();
            coupon.apply();
        }
    }    
}
```

### 안티 패턴
안티 패턴의 경우 꼬치꼬치 캐묻고 있습니다. 쿠폰 사용 기간이 만료되었는지, 이미 사용한 쿠폰인지 예제 코드는 이 정도로 단순하지만, 실제 실무에서는 이보다 더 많은 것들을 확인해야 합니다. 이 모든 세부적인 것들을 알고 확인하는 코드를 작성해야지 비로소 쿠폰 적용 코드를 완성할 수 있습니다.

더 중요한 것은 중복 코드입니다. 지금은 첫 구매에 대해서 할인 쿠폰 적용 로직이지만 앞으로 추가될 때마다 해당 로직이 중복으로 추가될 수밖에 없습니다. **해결 방법은 간단합니다. 객체의 상태를 스스로가 판단하고 결정할 수 있게 설계하는 것입니다.** 

### 좋은 패턴
`coupon.apply()` 메서드를 통해서 묻지 말고 시키고 있습니다. 쿠폰이 만료되었는지, 사용 여부 등을 묻지 않고 그냥 쿠폰을 적용하라고 지시만 하고 있습니다.

만약 새로운 쿠폰이 생기더라도 해당 쿠폰의 고유한 발급 조건을 `CouponIssueAble` 인터페이스를 상속해서 `canIssued()` 메서드를 적절하게 구현하고 `apply()` 메서드 호출을 통해서 쿠폰을 적용하면 됩니다. 물론 Production 레벨의 코드는 더 복잡하겠지만 전체적인 구조는 이해하기 쉬운 구조를 갖게 된다고 생각합니다.