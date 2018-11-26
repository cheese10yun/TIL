**해당 코드는 [Github](https://github.com/cheese10yun/blog-sample/tree/master/lombok)에 공개되어 있습니.**

Lombok은 자바 컴파일 시점에서 특정 어노테이션으로 해당 코드를 추가할 수 있는 라이브러리입니디. 이는 코드의 다이어트? 가독 성 및 유지 보수에 많은 도움이 됩니다. **하지만 편리한 만큼 잘못 사용하기 쉬운 것이 Lombok 입니다.** 

거창하게 실무에서 사용하는 Lombok이라고 표현했지만 어디까지 저의 주관적인 생각이기 때문에 각자 환경과 상황에 알맞게 사용하는 것이 바람직합니다. 지금 부터 제가 Lombok을 사용하는 방법에 대해서 소개해드리겠습니다. 간단한 예제를 위해서 JPA Entity 객체를 기반으로 설명드리겠습니다.

## @Data는 지양 하자

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

## 바람직한 Lombok 사용법

제가 생각하는 바람직한 Lombok 사용법입니다. 코드를 하나 씩 설명해보겠습니다.

```java
@Entity
@Table(name = "member")
@ToString(exclude = "coupons")
@Getter
@NoArgsConstructor(access = AccessLevel.PROTECTED)
@EqualsAndHashCode(of = {"id", "email"})
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

    @OneToMany
    @JoinColumn(name = "coupon_id")
    private List<Coupon> coupons = new ArrayList<>();

    @Builder
    public Member(String email, String name) {
        this.email = email;
        this.name = name;
    }
}
```

@ToString(exclude = "coupons"), @Getter 메서드는 위에서 설명드려 넘어가겠습니다. 



### @NoArgsConstructor 접근 권한을 최소화 하자

JPA에서는 프록시를 생성을 위해서 기본 생성자를 반드시 하나를 생성해야합니다. 이때 접근 권한이 protected 이면 됩니다. 굳이 외부에서 생성을 열어둘 필요가 없습니다. 

이렇게 디폴트 생성자 접근 권한을 설정하면 이러한 장점이 있습니다.

```java
@Entity
@Table(name = "product")
@Getter
@NoArgsConstructor(access = AccessLevel.PUBLIC) // 테스트를 위해 임시로 Public, 의도한 코드는 PROTECTED
public class Product {

    @Id
    private String id;

    private String name;

    @Builder
    public Product(String name) {
        this.id = UUID.randomUUID().toString();
        this.name = name;
    }
}
```
해당 코드는 ID 생성 전략을 UUID로 가져 갔습니다. 

![id-error](https://github.com/cheese10yun/blog-sample/raw/master/assets/id-error.png)

Id는 항상 null이 아니길 기대하지만 pulbic 생성자를 통해서 객체를 생성하면 Id 값은 null 이 되게 됩니다.

이처럼 기본 생성자를 아무 이유 없이 열어두는 것은 객체 생성 시 안전성을 심각하게 떨어트린다고 생각합니다.

이때 @NoArgsConstructor(access = AccessLevel.PUBLIC)를 사용하면 객체 생성 시 안전성을 어느 정도 보장받을 수 있습니다.

기본 생성자 접근을 protected으로 변경하면 외부에서 해당 생성자를 접근 할 수 없으므로 아래 생성자를 통해서 객체를 생성 해야 합니다.

```java
@Builder
public Product(String name) {
    this.id = UUID.randomUUID().toString();
    this.name = name;
}
```
해당 생성자 코드에는 UUID 생성 코드가 있어 객체를 생성할 시 반드시 Id 값을 보장받을 수 있습니다.

**객체에 대한 생성자를 하나로 두고 그것을 @Builder을 통해서 사용하는 것이 더군다나 효율적이라고 생각합니다.**

굳이 ID에 국환 되어 생각하지 객체 생성 시 반드시 생성되어야 하는 것들에 대한 안전성을 높이는 시각을 갖는 것이 중요하다고 생각합니다.

**클린 코드, 유지보수 하기 좋은 코드들을 이런 사소한 객체 생성부터 생각해보는 것이 많은 도움이 될 것이라고 생각합니다.**


![product-id-success](https://github.com/cheese10yun/blog-sample/raw/master/assets/product-id-success.png)

해당 테스트 코드가 통과되는 것을 확인할 수 있습니다.

### Builder 사용시 매개변수를 최소화 하자

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

## 결론
제가 생각한 것이 Best Practice 라고 생각하지 않습니다. 각자 환경과 상황에 알맞게 Lombok을 사용하는 것이 더욱 바람직하겠죠.

제가 말하고 싶은 부분은 클린 코드나, 유지 보수하기 좋은 코드, 좋은 캡슐화 이런 것들이 너무 거창한 것이 아니라 객체를 생성할 때나 메서드를 제공할 때 조금 더 깊게 생각해 보는 습관이 중요하다고 생각합니다.