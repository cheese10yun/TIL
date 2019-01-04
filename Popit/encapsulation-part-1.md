## 내가 생각하는 캡슐화란 (1)...

객체지향 개념에서 캡슐화는 정말 중요한 개념이라고 생각합니다. 캡슐화를 잘 지켜야 클래스 간의 결합도를 낮추어 코드를 유지 보수하기 쉽게 합니다.

이미 수많은 책이 이 개념에 관해서 설명하고 있습니다. 글을 읽을 때는 이해되지만 정작 캡슐화 좋은 코드를 작성하는 것은 또 다른 영역입니다.

저와 같은 주니어분들이 조금이라도 이해를 돕기 위해 제가 생각하는 캡슐화에 대해서 실무에서 많이 사용하는 Spring Boot, JPA 기반에서 설명해볼까 합니다.

## 목차
- [내가 생각하는 캡슐화란 (1)...](#%EB%82%B4%EA%B0%80-%EC%83%9D%EA%B0%81%ED%95%98%EB%8A%94-%EC%BA%A1%EC%8A%90%ED%99%94%EB%9E%80-1)
- [목차](#%EB%AA%A9%EC%B0%A8)
- [캡슐화의 정의](#%EC%BA%A1%EC%8A%90%ED%99%94%EC%9D%98-%EC%A0%95%EC%9D%98)
- [요구사항](#%EC%9A%94%EA%B5%AC%EC%82%AC%ED%95%AD)
- [캡슐화가 안좋은 안티 패턴](#%EC%BA%A1%EC%8A%90%ED%99%94%EA%B0%80-%EC%95%88%EC%A2%8B%EC%9D%80-%EC%95%88%ED%8B%B0-%ED%8C%A8%ED%84%B4)
  - [테스트 코드](#%ED%85%8C%EC%8A%A4%ED%8A%B8-%EC%BD%94%EB%93%9C)
  - [(1) Order의 getMessageTypes 메서드를 사용 할 때 불편하다](#1-order%EC%9D%98-getmessagetypes-%EB%A9%94%EC%84%9C%EB%93%9C%EB%A5%BC-%EC%82%AC%EC%9A%A9-%ED%95%A0-%EB%95%8C-%EB%B6%88%ED%8E%B8%ED%95%98%EB%8B%A4)
  - [(2) KAKAO를 KAOKO 라고 잘못 입력했을 경우](#2-kakao%EB%A5%BC-kaoko-%EB%9D%BC%EA%B3%A0-%EC%9E%98%EB%AA%BB-%EC%9E%85%EB%A0%A5%ED%96%88%EC%9D%84-%EA%B2%BD%EC%9A%B0)
  - [(3) 메시지에 KAKAO, EMAIL, SMS 처럼 공백이 들어 간다면 실패한다](#3-%EB%A9%94%EC%8B%9C%EC%A7%80%EC%97%90-kakao-email-sms-%EC%B2%98%EB%9F%BC-%EA%B3%B5%EB%B0%B1%EC%9D%B4-%EB%93%A4%EC%96%B4-%EA%B0%84%EB%8B%A4%EB%A9%B4-%EC%8B%A4%ED%8C%A8%ED%95%9C%EB%8B%A4)
  - [(4) 메시지가 없을 때 빈문자열("")을 보낼 경우](#4-%EB%A9%94%EC%8B%9C%EC%A7%80%EA%B0%80-%EC%97%86%EC%9D%84-%EB%95%8C-%EB%B9%88%EB%AC%B8%EC%9E%90%EC%97%B4%22%22%EC%9D%84-%EB%B3%B4%EB%82%BC-%EA%B2%BD%EC%9A%B0)
  - [(5) 메시지가 없을 때 null 을 보낼 경우](#5-%EB%A9%94%EC%8B%9C%EC%A7%80%EA%B0%80-%EC%97%86%EC%9D%84-%EB%95%8C-null-%EC%9D%84-%EB%B3%B4%EB%82%BC-%EA%B2%BD%EC%9A%B0)
  - [(6) 메시지가 중복으로 올경우](#6-%EB%A9%94%EC%8B%9C%EC%A7%80%EA%B0%80-%EC%A4%91%EB%B3%B5%EC%9C%BC%EB%A1%9C-%EC%98%AC%EA%B2%BD%EC%9A%B0)
  - [테스트 코드의 중요성](#%ED%85%8C%EC%8A%A4%ED%8A%B8-%EC%BD%94%EB%93%9C%EC%9D%98-%EC%A4%91%EC%9A%94%EC%84%B1)
- [좋은 캡슐화 패턴](#%EC%A2%8B%EC%9D%80-%EC%BA%A1%EC%8A%90%ED%99%94-%ED%8C%A8%ED%84%B4)
  - [Message of(Set<MessageType> types)](#message-ofsetmessagetype-types)
  - [public List<MessageType> getTypes()](#public-listmessagetype-gettypes)
  - [테스트 코드](#%ED%85%8C%EC%8A%A4%ED%8A%B8-%EC%BD%94%EB%93%9C-1)
  - [문제 해결](#%EB%AC%B8%EC%A0%9C-%ED%95%B4%EA%B2%B0)
  - [캡슐화를 통한 장점들](#%EC%BA%A1%EC%8A%90%ED%99%94%EB%A5%BC-%ED%86%B5%ED%95%9C-%EC%9E%A5%EC%A0%90%EB%93%A4)
- [웹 환경](#%EC%9B%B9-%ED%99%98%EA%B2%BD)
  - [Controller](#controller)
  - [Request](#request)
  - [테스트 코드](#%ED%85%8C%EC%8A%A4%ED%8A%B8-%EC%BD%94%EB%93%9C-2)
  - [정상요청](#%EC%A0%95%EC%83%81%EC%9A%94%EC%B2%AD)
  - [요청바디가_유효하지않을경우](#%EC%9A%94%EC%B2%AD%EB%B0%94%EB%94%94%EA%B0%80%EC%9C%A0%ED%9A%A8%ED%95%98%EC%A7%80%EC%95%8A%EC%9D%84%EA%B2%BD%EC%9A%B0)
  - [값 확인](#%EA%B0%92-%ED%99%95%EC%9D%B8)
- [참고](#%EC%B0%B8%EA%B3%A0)


## 캡슐화의 정의
> 캡슐화는 정보은닉을 통해 높은 응집도와 낮은 결합도를 갖도록 한다. 정보 은닉이란 말 그대로 알 필요가 없는 정보는 외부에서 접근하지 못하도록 제한하는 것이다.

**여기서 중요한 키워드는 `높은 응집도`, `낮은 결합도`, `정보 은닉` 입니다.** 핵심 키워들 기반으로 설명을 진행하겠습니다.

## 요구사항 
* 주문을 신청할 때 배송 출발시 받을 메시지 플랫폼을 N개 선택 할 수 있다.
* 메시지 플랫폼은 KAKAO, SMS, EMAIL 등이 있다.
* 메시지 플랫폼은 지속적으로 추가 될 수 있다.

## 캡슐화가 안좋은 안티 패턴
```java
@Entity
@Table(name = "product")
@NoArgsConstructor(access = AccessLevel.PROTECTED)
@Getter
public class Order {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private long id;

    @Column(name = "anti_message_types")
    private String messageTypes;

    @Builder
    public Order(String messageTypes) {
        this.messageTypes = messageTypes;
    }
}
```
* Order 엔티티 객체가 있습니다. 
* Order 엔티티는 주문을 완료시 메시지 플랫폼을 받을 수 있는 메시지 타입을 저장할 `messageTypes` 멤버 필드를 갖습니다.
  * **데이터베이스 정규화는 생략했습니다. 저런 타입일 경우 정규화의 대상이 된다고 생각하지 않습니다. 이 부분은 크게 생각 안하시고 순수하게 캡슐화의 관점에서 설명드리겠습니다.**
* 복수개의 문자열이 들어 오기 때문에 `","` 기반으로 문자열을 split 합니다.

### 테스트 코드


```java
public class OrderUnitTest {

    @Test
    // (1) Order의 getMessageTypes 메서드를 사용 할 때 불편하다
    // 안좋은 캡술화
    public void anti_message_test_01() {
        final Order order = build("KAKAO,EMAIL,SMS");
        final String[] split = order.getMessageTypes().split(",");

        assertThat(split, hasItemInArray("KAKAO"));
        assertThat(split, hasItemInArray("EMAIL"));
        assertThat(split, hasItemInArray("SMS"));
    }

    @Test
    // (2) KAKAO를 KAOKO 라고 잘못 입력했을 경우
    public void anti_message_test_02() {
        final Order order = build("KAOKO,EMAIL,SMS");
        final String[] split = order.getMessageTypes().split(",");

        assertThat(split, not(hasItemInArray("KAKAO")));
        assertThat(split, hasItemInArray("EMAIL"));
        assertThat(split, hasItemInArray("SMS"));
    }

    @Test
    // (3) 메시지에 KAKAO, EMAIL, SMS 처럼 공백이 들어 간다면 실패한다
    public void anti_message_test_03() {
        final Order order = build("KAKAO, EMAIL, SMS");
        final String[] split = order.getMessageTypes().split(",");

        assertThat(split, hasItemInArray("KAKAO"));
        assertThat(split, not(hasItemInArray("EMAIL")));
        assertThat(split, not(hasItemInArray("SMS")));
    }


    @Test
    // (4) 메시지가 없을 때 빈문자열("")을 보낼 경우
    public void anti_message_test_04() {
        final Order order = build("");
        final String[] split = order.getMessageTypes().split(",");

        assertThat(split, hasItemInArray(""));
    }


    @Test(expected = NullPointerException.class)
    // (5) 메시지가 없을 때 null 을 보낼 경우
    public void anti_message_test_05() {
        final Order order = build(null);
        order.getMessageTypes().split(",");
    }

    @Test
    // (6) 메시지가 중복으로 올경우
    public void anti_message_test_06() {
        final Order order = build("KAKAO, KAKAO, KAKAO");
        final String[] split = order.getMessageTypes().split(",");

        assertThat(split, hasItemInArray("KAKAO"));
        assertThat(split.length, is(3));
    }
}
```

### (1) Order의 getMessageTypes 메서드를 사용 할 때 불편하다
가장 쉽게 생각할 수 있는 방법입니다. 단순히 getter 메서드를 이용해서 외부 객체가 사용하게 제공합니다. 이는 캡슐화에 엄청난 악영향을 미치게 됩니다.

우선 `getMessageTypes()` 메서드를 사용 하는 모든 곳에서 `split()` 메서드를 이용해서 메시지 타입을 배열로 만들어서 사용해야합니다.

### (2) KAKAO를 KAOKO 라고 잘못 입력했을 경우
단순하게 String을 사용하기 때문에 type safe 하지 않습니다. KAKAO를 KAOKO로 잘못 입력해도 제대로 검증하기 어려우며 검증하는 로직을 추가 하더라도 해당 에러는 Runtime으로 넘어가게 됩니다. 이처럼 단순 문자열이면 이러한 단점을 갖게 됩니다.


### (3) 메시지에 KAKAO, EMAIL, SMS 처럼 공백이 들어 간다면 실패한다
일반적으로 웹 개발을 하면 대부분의 요청은 컨트롤러에서 받게 됩니다. 이 때 KAKAO, EMAIL, SMS 문자열 사이에 빈 공백이 들어오게 되면 `split()` 함수가 제대로 동작하지 않습니다. 테스트 코드를 보면 `EMAIL`, `SMS`는 `not(hasItemInArray..)` 으로 검증됩니다. 

물론 앞뒤 공백을 자르는 로직이 추가되면 되지만 이렇게 되면 점점 로직의 복잡도가 높아지게 됩니다.


### (4) 메시지가 없을 때 빈문자열("")을 보낼 경우
컨트롤러 요청을 받을 때 받을 메시지 플랫폼이 없다면 `""` 으로 받게 됩니다. 이 빈 공백 이라는 것이 의미하는 게 어떤 메시지 플랫폼도 선택하지 않았다는 의미로 해석되기는 어렵습니다. **문자열 자체는 이러한 타입에 적합하지 않기 때문에 메시지가 없을 때 어떤 식으로 처리해야 할지 고민하게 됩니다.**

### (5) 메시지가 없을 때 null 을 보낼 경우
빈 공백의 의미가 정확하지 않다고 생각했을 경우 null로 요청을 받게 되면 `split()` 메서드에서 RuntimeException이 발생하게 됩니다. 물론 로직을 추가해서 null인 경우를 처리할 수 있지만, 이것은 3번에서 언급했던 것처럼 계속 코드의 복잡성이 높아지게 됩니다.

### (6) 메시지가 중복으로 올경우
메시지 플랫폼이 중복으로 넘어오게 될 실제 메시지가 중복으로 발송되기 때문에 로직을 추가 해야 됩니다.

### 테스트 코드의 중요성
캡슐화에서 벗어난 주제이기 때문에 테스트 코드에 대한 부가적인 설명해 드리는 것이 조금은 어색하지만, 테스트 코드의 중요성을 한번 언급하고 싶었습니다.

> 문제 있는 코드를 빨리 파악할 수 있다.

해당 기능의 테스트 코드를 작성하면 이 코드의 문제점을 가장 빠르게 파악할 수 있습니다. 저는 코드가 왜 캡슐화에 안 좋은 코드인지 테스트 코드를 통해서 알게 됐습니다. 위에서 언급한 1~5 문제들을 테스트 코드 작성 시 파악했고 리팩토링 작업을 진행했습니다.

**테스트 코드는 냄세나는 코드를 빠르게 찾게 해줍니다. 이것도 테스트 코드의 엄청난 장점이라고 생각합니다.**



## 좋은 캡슐화 패턴
```java
public class Order {
    ...
    @Embedded
    private Message message;
}

@Embeddable
@NoArgsConstructor(access = AccessLevel.PROTECTED)
public class Message {

    @Column(name = "message_type")
    private String type;

    private Message(String type) {
        this.type = StringUtils.isEmpty(type) ? null : type;
    }

    public static Message of(Set<MessageType> types) {
        return new Message(joining(types));
    }

    public List<MessageType> getTypes() {
        if (StringUtils.isEmpty(type)) {
            return new ArrayList<>();
        }

        return new ArrayList<>(doSplit());
    }

    private static String joining(Set<MessageType> types) {
        return types.stream()
                .map(Enum::name)
                .collect(Collectors.joining(","));
    }


    private Set<MessageType> doSplit() {
        final String[] split = this.type.split(",");
        return Arrays.stream(split)
                .map(MessageType::valueOf)
                .collect(Collectors.toSet());
    }
}

public enum MessageType {
    EMAIL, SMS, KAKAO;
}
```


### Message of(Set<MessageType> types)
기본 생성자를 private 메서드로 지정했기 때문에 외부에서 해당 객체를 생성 할 수 있는 유일한 방법은 `of()` 메서드를 이용하는 방법뿐입니다. 

이처럼 객체 생성도 최대한 제한해서 객체를 올바르게 생성할 수 있도록 제공해야 합니다. 이로써 좋은 캡슐화가 진행되고 있습니다.

joining(types) 메서드를 통해서 넘겨받은 Set 자료형 types를 String 객체로 변경했습니다. 실제 데이터베이스에 문자열 자료형으로 저장하게 됩니다.


### public List<MessageType> getTypes()

위에서 언급한 정보 은닉 개념입니다.

> 정보 은닉이란 말 그대로 알 필요가 없는 정보는 외부에서 접근하지 못하도록 제한하는 것이다.


여기서 말하는 알 필요 없는 정보는 실제 데이터베이스에는 메시지 타입이 `","` 기준으로 메시지 타입을 구분하고 있다는 점입니다.

getTypes 리턴 타입은 List이기 때문에 외부에서는 절대 데이터베이스에 저장돼 있는 평문 문자 `"KAKAO,SMS,EAML"` 문자열을 접근할 수 없습니다.

**그것보다 더 중요한 건 데이터베이스에 어떤 형식으로 저장돼있는지 세부적인 것들은 관심 대상이 아니게 됩니다.**

내가 필요할 때 메시지 타입을 편하게 List 형식으로 가져다 사용하면 됩니다. 이것이 캡슐화라고 생각합니다.

### 테스트 코드

```java
public class MessageTest {

    @Test
    public void 메시지_타입이_EMAIL_KAKAO_SMS_일경우() {
        final Set<MessageType> types = new HashSet<>();
        types.add(MessageType.EMAIL);
        types.add(MessageType.KAKAO);
        types.add(MessageType.SMS);

        final Message message = Message.of(types);

        assertThat(message.getTypes(), hasItem(MessageType.EMAIL));
        assertThat(message.getTypes(), hasItem(MessageType.KAKAO));
        assertThat(message.getTypes(), hasItem(MessageType.KAKAO));
        assertThat(message.getTypes(), hasItem(MessageType.SMS));
        assertThat(message.getTypes(), hasSize(3));
    }

    @Test
    public void 메시지_타입이_EMAIL_KAKAO일경우() {
        final Set<MessageType> types = new HashSet<>();
        types.add(MessageType.EMAIL);
        types.add(MessageType.KAKAO);

        final Message message = Message.of(types);

        assertThat(message.getTypes(), hasItem(MessageType.EMAIL));
        assertThat(message.getTypes(), hasItem(MessageType.KAKAO));
        assertThat(message.getTypes(), not(hasItem(MessageType.SMS)));
        assertThat(message.getTypes(), hasSize(2));
    }

    @Test
    public void 메시지_타입이_없을경우() {
        final Set<MessageType> types = Collections.emptySet();
        final Message message = Message.of(types);

        assertThat(message.getTypes(), hasSize(0));
    }

    @Test
    public void 메시지_타입이_중복되는경우() {
        final Set<MessageType> types = new HashSet<>();
        types.add(MessageType.EMAIL);
        types.add(MessageType.EMAIL);
        types.add(MessageType.EMAIL);

        final Message message = Message.of(types);

        assertThat(message.getTypes(), hasItem(MessageType.EMAIL));
        assertThat(message.getTypes(), not(hasItem(MessageType.SMS)));
        assertThat(message.getTypes(), not(hasItem(MessageType.KAKAO)));
        assertThat(message.getTypes(), hasSize(1));
    }
}
```
테스트 코드를 보시면 항상 올바른 데이터만 입력할 수 있고, 그것을 검증하는 것도 단순해졌고 이해하고 예측하기 쉬워졌습니다. 이런 것이 좋은 캡슐화라고 생각합니다.


### 문제 해결

* (1) Order의 getMessageTypes 메서드를 사용 할 때 불편하다
  * 사용하는 곳에서 리스트를 만드는 것아 이나리 getTypes()의 리턴자료형이 List이기 때문에 사용하기 편합니다.
* (2) KAKAO를 KAOKO 라고 잘못 입력했을 경우
  * enum 자료형을 사용했기 때문에 유효하지 않은 데이터를 입력할 수 없습니다.
* (3) 메시지에 KAKAO, EMAIL, SMS처럼 공백이 들어간다면 실패한다
  * enum 자료형을 사용했기 때문에 공백처럼 유효하지 않은 데이터를 입력할 수 없습니다.
* (4) 메시지가 없을 때 빈 문자열("")을 보내면
  * enum 자료형을 사용했기 때문에 공백처럼 유효하지 않은 데이터를 입력할 수 없습니다.
* (5) 메시지가 없을 때 null을 보낼 경우
  * 비어 있을 경우 null 아 아닌 빈 Set 객체를 넘기면 되기 때문에 null 자체를 입력하게 될 이유가 없어졌습니다.
* (6) 메시지가 중복으로올 경우 
  * 자료형이기 때문에 데이터가 중복으로 입력되더라도 최종적으로는 중복을 제거하게 됩니다.

### 캡슐화를 통한 장점들

* 위에서 작성한 코드 구조상 유효하지 않은 메시지 타입을 강제로 입력하고 싶더라도 어렵습니다. 
* 실제 데이터베이스에 입력돼 있는 문자열을 가져올 수도 없으며, **어떤 형식으로 저장돼 있는지 관심을 가질 필요가 없게 되었습니다.**
* 메시지 타입이 없는 경우 컬렉션이 empty이기 때문에 보다 명확합니다.
* 응집도가 높아졌습니다. 메시지에 대한 세부 로직들이 Order에서 분리되고 Message 객체에 응집해 있습니다.
* 재사용성이 높아졌습니다. 만약 상품 등록이 성공했을 경우 메시지 플랫폼을 통해서 응답받고 싶다면 Product에서 Message 객체를 선언하기만 하면 됩니다.


## 웹 환경
웹 환경에서 추가적으로 설명드리겠습니다.

### Controller
```java
@RestController
@RequestMapping("/orders")
public class OrderApi {

    private final OrderRepository orderRepository;

    @PostMapping
    public Order create(@RequestBody @Valid OrderRequest request) {
        final Order order = buildOrder(request);
        return orderRepository.save(order);
    }
}

@Getter
public class OrderRequest {
    @NotNull
    private Set<MessageType> messageType;
}
```

### Request

![](https://github.com/cheese10yun/blog-sample/blob/master/encapsulation/assets/swagger-message-request.png?raw=true)

요청은 배열으로 받게 합니다. 만약 받을 메시지가 없다면 빈 배열로 넘깁니다.


### 테스트 코드


```java
@RunWith(SpringRunner.class)
@SpringBootTest
@AutoConfigureMockMvc
public class OrderApiTest {
    @Autowired
    private ResourceLoader resourceLoader;

    @Autowired
    private MockMvc mvc;
    
    @Test
    public void 정상요청() throws Exception {
        final String json = readJson("valid-request.json");
        mvc.perform(post("/orders")
                .contentType(MediaType.APPLICATION_JSON_UTF8)
                .content(json))
                .andDo(print())
                .andExpect(status().isOk());
    }

    @Test
    public void 요청바디가_유효하지않을경우() throws Exception {
        final String json = readJson("invalid-request.json");
        mvc.perform(post("/orders")
                .contentType(MediaType.APPLICATION_JSON_UTF8)
                .content(json))
                .andDo(print())
                .andExpect(status().isBadRequest());
    }
}
```

### 정상요청

```json
{
  "messageType": [
    "EMAIL", "SMS"
  ]
}
```
모든 값이 유효합니다. 200을 응답 받습니다.

### 요청바디가_유효하지않을경우 
```json
{
  "messageType": [
    "EMAIL", "KKA"
  ]
}
```
유효하지 않은 값일 경우 400 응답을 받게됩니다.

### 값 확인

![](https://github.com/cheese10yun/blog-sample/blob/master/encapsulation/assets/string-value.png?raw=true)

배열 형식의 받을 입력 받고 응답해주지만, 실제 값은 `","`으로 구분하는 문자열입니다. 

**다시 한번 강조하지만 외부 객체에서는 저 문자열을 가져올 수 없을 뿐만 아니라 실제 데이터베이스에 문자열로 저장돼있는지 관심조차 가질 필요가 없습니다. `getTypes()` 메서드로 List형으로 외부에 제공해주기만 하면 됩니다.** 이것이 캡슐화의 기본적 개념이라고 생각합니다.

## 참고
[Github - Issue](https://github.com/cheese10yun/blog-sample/issues/1) 정보 은닉(information hiding) 의미 오류에 대해서 gyuwon 님이 친절하게 코멘트 달아주신 부분도 참고하시면 좋을거 같습니다.
