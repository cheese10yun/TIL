## 1. @Data는 지양하자

```java
@Entity
@Table(name = "member")
@Data
public class Member {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private long id;

    @Column(name = "email", nullable = false)
    private String email;

    @Column(name = "name", nullable = false)
    private String name;

    @CreationTimestamp
    @Column(name = "create_at", nullable = false, updatable = false)
    private LocalDateTime createAt;

    @UpdateTimestamp
    @Column(name = "update_at", nullable = false)
    private LocalDateTime updateAt;
}
```

@Data는 @ToString, @EqualsAndHashCode, @Getter, @Setter, @RequiredArgsConstructor을 한번에 사용하는 강력한 어노테이션 입니다. 강력한 어노테이션인 만큼 그에 따른 부작용도 많다고 생각합니다.

### 무분별한 Setter 남용 
위에서 언급했듯이 @Data를 사용하면 자동으로 Setter를 지원하게 됩니다. 그로 인해서 생기는 문제점들이 있습니다. 이전에 [Setter 사용하지 않기](https://github.com/cheese10yun/spring-jpa-best-practices/blob/master/doc/step-06.md)에서 한번 다루었던 내용입니다. 

**간단하게 정리하면 Setter는 그 의도가 분명하지 않고 객체를 언제든지 변경할 수 있는 상태가 되어서 객체의 안전성이 보장받기 힘듭니다.** 위 코드에서 email의 변경 기능이 제공 되지 않는다고 가정한다면 email 관련된 setter도 제공되지 않아야 안전합니다. 단순 안전함을 넘어서 해당 객체가 자기 자신을 가장 잘 표현하는 구조 즉 email의 변경 포인트를 제공하지 않음으로써 email 변경 기능이 없다는 것을 표현한다고 생각합니다.

### ToString으로 인한 양방향 연관관계시 순환 참조 문제

```java
@Entity
@Table(name = "member")
@Data
public class Member {
    ....
    @OneToMany
    @JoinColumn(name = "coupon_id")
    private List<Coupon> coupons = new ArrayList<>();
}

@Entity
@Table(name = "coupon")
@Data
public class Coupon {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private long id;

    @ManyToOne
    private Member member;

    public Coupon(Member member) {
        this.member = member;
    }
}

```

![lombokc-toString-error](https://github.com/cheese10yun/blog-sample/raw/master/assets/lombokc-toString-error.png)


위 코드 코드처럼 Member 객체와 Coupon 객체가 양방향 영관관계일 경우 ToString을 호출하면 무한 순환 참조가 발생합니다. JPA를 사용하다 보면 객체를 Json으로 직렬화 하는 가정에서 발생하는 문제와 동일한 이유입니다. 이 처럼 무분별하게 @Data를 사용하게 되면 이러한 문제를 만나기 쉽습니다. 

쉬운 해결 방법으로는 

```java
@ToString(exclude = "coupons")
public class Member {...}
```
해당 어노테이션을 이용해서 ToString 항목에서 제외시키는 것입니다.

![lombok-toString-sueecess](https://github.com/cheese10yun/blog-sample/raw/master/assets/lombok-toString-sueecess.png)

해당 테스트 코드가 정상적으로 동작하는 것을 확인할 수 있습니다. 

정말 깊게 생각한다면 @Getter도 바람직하지는 않다고 생각합니다. 모든 멤버필드에 대해서 Getter를 제공해주는 것은 캡슐화에 좋은 영향을 준다고 생각하지 않습니다. 무분별하게 Getter를 제공해주면 객체를 사용하는 곳에서 get 메서드를 이용해서 로직들을 구현하는 경우가 있습니다. 사실 이러한 기능들은 해당 객체가 캡슐화해서 제공해주는 것이 바람직합니다.

**하지만 이렇게까지 설계하는 것은 현실적으로 어렵다고 생각합니다.** 그래서 @Getter는 사용하되 최대한 객체가 캡슐화하여 해당 객체가 그 기능을 제공해주는 것이 바람직합니다.

### @EqualsAndHashCode 의 남발..

```java
public boolean equals(final Object o) {
    if (o == this) {
      return true;
    } else if (!(o instanceof Member)) {
      return false;
    } else {
      Member other = (Member)o;
      if (!other.canEqual(this)) {
        return false;
      } else if (this.getId() != other.getId()) {
        return false;
      } else {
        label73: {
          Object this$email = this.getEmail();
          Object other$email = other.getEmail();
          if (this$email == null) {
            if (other$email == null) {
              break label73;
            }
          } else if (this$email.equals(other$email)) {
            break label73;
          }

          return false;
        }

        Object this$name = this.getName();
        Object other$name = other.getName();
        if (this$name == null) {
          if (other$name != null) {
            return false;
          }
        } else if (!this$name.equals(other$name)) {
          return false;
        }
        ....
    }
  }
```

@EqualsAndHashCode 멤버 필드 전체애 대한 equals 메서드가 진행됨(성능 이슈), 성능 이슈가 Set 자료 구조를 사용할 경우, 기타등등 이슈가 발생할 수 있음, HashCode 생성하는 코드도 동일한 문제


## 3. 클래스 상단의 @Builder는 지양 하자
```java
@Builder
public class Member {...}
```
클래스 위에 @Builder를 사용 시 @AllArgsConstructor 어노테이션을 붙인 효과를 발생시켜 **모든 멤버 필드에 대해서 매개변수를 받는 기본 생성자를 만듭니다.**


Builder AllArgsConstructor는 무슨 문제는 다음과 같습니다.

![all-arg-builder](https://github.com/cheese10yun/blog-sample/raw/master/assets/all-arg-builder.png)

위 그림처럼 모든 멤버필드에 대한 매개변수를 허용하게 됩니다. 

Member의 Id 생성전략은 데이터베이스의 auto_increment를 의존하고 있다고 가정했을 경우 Id를 넘겨받는 않아야 합니다.

또 createAt, updateAt 같은 경우는 @CreationTimestamp, @UpdateTimestamp 각각의 어노테이션이 해당 일을 담당하고 있습니다. 이 처럼 객채 생성시 받지 않아야 할 데이터들이 클래스 상단 @Builder를 사용하게 되면 발생하게 됩니다.

```java
public class Member {

    @Builder
    public Member(String email, String name) {
        this.email = email;
        this.name = name;
    }
}
```
이렇게 받아야 하는 생성자를 필요조건에 따라 지정하고 그 위에 @Builder를 붙이는게 바람직합니다.

![all-arg-builder-2](https://github.com/cheese10yun/blog-sample/raw/master/assets/all-arg-builder-2.png)

위 그림처럼 매개변수 name, email만 넘겨 받을 수 있게 됩니다.

## 3.생성자위 Builder에 적절한 책임을 부여하자


```java
@Entity
@Table(name = "refund")
@Getter
@NoArgsConstructor(access = AccessLevel.PROTECTED)
public class Refund {

  @Id
  @GeneratedValue(strategy = GenerationType.IDENTITY)
  private long id;

  @Embedded
  private Account account;

  @Embedded
  private CreditCard creditCard;

  @OneToOne
  @JoinColumn(name = "order_id", nullable = false, updatable = false)
  private Order order;


  @Builder(builderClassName = "ByAccountBuilder", builderMethodName = "ByAccountBuilder") // 계좌 번호 기반 환불, Builder 이름을 부여해서 그에 따른 책임 부여, 그에 따른 필수 인자값 명확
  public Refund(Account account, Order order) {
    Assert.notNull(account, "account must not be null");
    Assert.notNull(order, "order must not be null");

    this.order = order;
    this.account = account;
  }

  @Builder(builderClassName = "ByCreditBuilder", builderMethodName = "ByCreditBuilder")  // 신용 카드 기반 환불, Builder 이름을 부여해서 그에 따른 책임 부여, 그에 따른 필수 인자값 명확
  public Refund(CreditCard creditCard, Order order) {
    Assert.notNull(creditCard, "creditCard must not be null");
    Assert.notNull(order, "order must not be null");

    this.order = order;
    this.creditCard = creditCard;
  }
}

```
주문에 대한 환불이 있을 경우 환불에 대한 금액을 신용 카드 취소, 계좌 기반 환불이 있을 수 있습니다. 신용 카드 결제 취소일 경우에는 신용 카드 정보를 받아야 하고(실제 이런식으로 신용 카드 환불이 진행되지는 않습니다.), 계좌 정보를 입력받아야 하는 경우 하나의 Builder인 경우에는 필수 값 검증이 어렵습니다.

신용 카드 정보와, 계좌 정보가 같이 넘어오면 어떻게 할 것인가? 이런 문제부터 생각할 것들이 많습니다. 이런 경우 아래와 같이 Builder의 이름을 명확하게 해서 책임을 부여하는 것이 좋습니다.

```java
public class RefundTest {
    ...
    ...

  @Test
  public void ByAccountBuilder_test() {
    final Refund refund = Refund.ByAccountBuilder() // 빌더 이름으로 명확하게 그 의도를 드러 내고 있습니다.
        .account(account)
        .order(order)
        .build();

    assertThat(refund.getAccount()).isEqualTo(account);
    assertThat(refund.getOrder()).isEqualTo(order);
  }

  @Test
  public void ByCreditBuilder_test() {
    final Refund refund = Refund.ByCreditBuilder() // 빌더 이름으로 명확하게 그 의도를 드러 내고 있습니다.
        .creditCard(creditCard)
        .order(order)
        .build();

    assertThat(refund.getCreditCard()).isEqualTo(creditCard);
    assertThat(refund.getOrder()).isEqualTo(order);
  }
}
```

## 4.@Builder.Default는 지양하자

```java
@Getter
@Builder
@ToString
public class Member {

  @Builder.Default
  private String name = "yun";

  @Builder.Default
  private String email = "yun.cheese@kakao.com";

  private String nickName = "asd";

  private List<Member> members = new ArrayList<>();

}
```

```java
public class MemberTest {

  @Test
  public void test() {

    final Member member = Member.builder().build();
    final Member member1 = Member.builder().email("kakapay").name("kakapay").build();

    System.out.println(member); // Member(name=yun, email=yun.cheese@kakao.com, nickName=null, members=null)
    System.out.println(member1); // Member(name=kakapay, email=kakapay, nickName=null, members=null)

  }
}
```

코드의 예측이 어려워진다. @Builder.Default 어노테이션이 없는 멤버 필드는 Builder로 생성시 할당 받지 못한다. POJO 스럽게 객체를 보는 것이 가장 가독성 및 이해하기 어려움




```
warning: @Builder will ignore the initializing expression entirely. If you want the initializing expression to serve as default, add @Builder.Default. If it is not supposed to be settable during building, make the field final.
  private String nickName = "asd";
```


## 5.생성자 접근 지시자는 최소한으로 하자
```java
@Entity
@Table(name = "refund")
@NoArgsConstructor(access = AccessLevel.PROTECTED)
public class Refund { ... }
```

엔티티 객체는 JPA에서 프록시 객체가 필요함으로 기본 생성자 접근지시자가 최소 PROTECTED 이다.

```java
@NoArgsConstructor(access = AccessLevel.PRIVATE)
public class RefundRequest {...}
```
Request 객체는 HttpMessageConverter에서 리플랙션 기반으로 직렬화 작업을 진행하기 때문에 기본 생성자가 필요 이때 최소 접근시시자가 PRIVATE, `@NoArgsConstructor(access = AccessLevel.PRIVATE)` 작성하지 않고, 이후 테스트 코드 작성을 위해 생성자를 추가시, 기본 생성자가 없으므로 예외 발생 가능성이 있음 **생성자에 대한 접근시시자는 최소한으로 하는 것이 좋다.**


## 6. exclude 보다는 of
@ToString, @EqualsAndHashCode 어노테이션 처럼 exclude 으로 특정 필드를 제거 하더라더 멤버 필드 추가시 해당 필드는 자동으로 해당 어노테이션에 적용되기 때문에 of로 선택적으로 (멤버 필드가 추가되더라도 수동으로) 로직에 적용하는 것이 좋다.


## 7. lombok.config 설정을 통해서 제한 하자
lombok.config 설정 파일을 통해서 lombok 어노테이션을 제한 할 수 있습니다. 위에서 언급한 @Data 등 사용을 했을 경우 위험 부담이 있는 어노테이션들은 해당 설정에서 제한 할 수 있습니다.


```
lombok.Setter.flagUsage = error
lombok.AllArgsConstructor.flagUsage = error
lombok.ToString.flagUsage = warning
lombok.data.flagUsage= error
```


![](https://github.com/cheese10yun/blog-sample/raw/master/lombok/assets/lombok-config.png?raw=true)


실제 컴파일을 진행하면 위에서 설정한 lombok.config에서 제한한 어노테이션은 warning, error로 표시됩니다. 이 처럼 명확한 가이드 라인이 있으면 설정 파일을 통해서 제한하는 것이 바람직하다고 생각합니다.

## 8. 관련 코드
* [실무에서 Lombok 사용법](https://github.com/cheese10yun/blog-sample/tree/master/lombok)
* [Builder 기반으로 객체를 안전하게 생성하는 방법](https://github.com/cheese10yun/blog-sample/tree/master/ddd)