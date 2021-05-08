# 스프링 부트 2.0 마이크로서비스와 리액티브 프로그래밍 정리
> 출처 [스프링 부트 2.0 마이크로서비스와 리액티브 프로그래밍](http://www.kyobobook.co.kr/product/detailViewKor.laf?ejkGb=KOR&mallGb=KOR&barcode=9791161752624&orderClick=LAA&Kc=)을 보고 정리한 자료입니다.

## Rabbmit MQ란 ?
래빗MQ는 오픈소스 AMQP 브로커다. AMQP는 유선을 통해 전송되는 메시지 형식을 포함하는 개발형 프로토콜이며, JMS와 같은 다른 메시징 솔루션에 비해 인기가 높다.

JMS는  API, AMQP는 프로토콜이다. JMS는 메시지의 형식이 아닌 브로커와 통신하는 방법을 정의한다. 또한 자바 애플리케이션에만 국한돼 있다. AMQP는 브로커와 통신하는 방법에 대해서 논하지 않지만. 메시지가 유선을 통해 큐에 어떻게 넣고 꺼내지는지에 대해 정의한다.

서로 다른 두 가지 애플리케이션을 생각해보자, 둘 다 자바면 JVMS를 통해 통신할 수 있지만 이중에 하나가 루비라면 JMS는 사용하지 못할것이다.

JMS 발송 브로커는 매시지 전송을 위해 AMQP를 사용할 수 있어 JMS와 AMQP의 차이점을 더 확연하게 해준다. 


## AMQP 기본사항
AMQP-기반 생상자는 큐에 직접 게시하지 않고 익스체인지에 게시한다. 큐가 선언되면 익스체인지에 바인딩돼야 한다. 여러 큐를 동일한 익스체인지에 바인딩해 토픽 개념을 애쿨레이션 할 수 있다.

토픽 익스체인지를 통해 라우팅 키는 `comment.*`와 같은 와일드 카드를 사용할 수 있다. 이 상황은 사용자가 조건을 제공할 때 까지 실제 라우팅 키를 알 수 없는 클라이언트에 가장 적합하다.


```java
@RabbitListener(bindings = @QueueBinding(
    value = @Queue,
    exchange = @Exchange(value = "learning-spring-boot"),
    key = "comments.new"
))
public void save(Comment comment){
    commentRepository.save(comment)
}
```
* `@RabbitListener` 어노테이션은 메시지를 사용하는 방법을 등록하는 가장 쉬운 방법이다.
* `@QueueBinding` 어노테이션은 큐와 익스체인지를 즉시 선언하는 가장 쉬운 방법이다. 이 경우 이 메서드에 대한 익명 큐를 생성하고 leaning-spring-boot 익스체인지에 바인딩한다.
* 이 메서드는 라우팅 키는 `comments.new`이며, 이는 leanirng-spring-boot 익스체인지에 게시된 메시지가 이 메서드를 호출하게 한다는 것을 의미한다.
* `@RabbitListener` 메서드는 스프링 AMQP Message 스프링 메시징 Message 다양한 메시지 헤더, 평범하고 오래된 자바 객체를 받을 수 있다.
* 메서드 자체는 `CommentWriterRepository`를 호출해서 실제로 데이터 저장소에 코멘드틀 저장한다.

래빗MQ를 사용하려면 일반적으로 @EnableRabbit이 필요하지만 스프링 부트 덕분에 Spring-boot-stater-amqp가 클래스 패스에 있으면 자동으로 활성화된다.

@RabbitListener를 사용하면 운영에 필요한 모든 익스체인지와 큐를 동적으로 생성할 수 있다는 점이다. 


스프링 AMQP 메시지 객체를 제공하기 위해 메서드 시그니처를 선언했다면 바이트 배열을 풀수 있을 것이다. **그러나 스프링 AMQP는 도메인 객체를 직렬화하는 기능이 제한돼 있다. 아무런 노력을 기울리지 않고도 간단한 문자열과 직렬화 처리할 수 있다.**


# RabbitMQ와 AMQP 기본

> 출처 [RabbitMQ와 Spring AMQP를 이용한 간단한 작업 큐 만들기](http://blog.naver.com/PostView.nhn?blogId=tmondev&logNo=220419853534&parentCategoryNo=&categoryNo=6&viewDate=&isShowPopularPosts=false&from=postView)를 보고 정리한 내용입니다.


## AMQP
프로그래밍에서 MQ는 프로세스 또는 프로그램 인스턴스가 데이터를 서로 교환할 때 사용하는 방법을 말한다. 이러한 MQ를 오픈소스에 기반한 표준 프로토콜이 AMQP이다. AMQP 자체가 프로토콜을 의미하기 때문에 이 프로토콜을 구현한 MQ 제품들은 여러가지가 있으며 그 중하나가 RabbitMQ이다.

### AMQP의 구성요소와 라우팅 알고리즘
![](/assets/rabbitmq.png)

* AMQP의 라우팅 모델은 Exchange, Queue, Binding으로 구성된다.

| 이름       | 설명                                             |
|----------|------------------------------------------------|
| Exchange | Publisher(Producer)로부터 수신한 메시지를 큐에 분배하는 라우터 역할 |
| Queue    | 메시지를 메모리나 디스크에 저장했다가 Cusomer에게 메시지를 전달하는 역할    |
| Binding  | Exchange와 Queue의 관계를 정의한 것                     |


### Exchagne Type
Exchagne Type은 메시지를 어떤 방법으로 라우팅할지 결정하는 일종의 알고리즘 말하며 AMQP에서는 다음과 같은 Exchange Type을 정의하고 있다.

| Exchagne Type    | 정의                                                                                                                                        |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------|
| Direct exchagne  | Exchagne에 바인된 Queue중에 메시지의 라우팅 키와 매핑되어 있는 Queue로 메시지를 전달한다. 1:1 관계로 Unicast 방식에 접합하며, 주로 라운드 로빈 방식으로 여러 workers(Cusumer)간 Task를 분리에 사용된다. |
| Fanout Exchagne  | 메시지의 라우팅 키를 무시하고 Exchagne에 바인딩된 모든 Queue에 메시지를 전달한다. 1:N 관계로 메시지를 브로드캐스트하는 용도로 사용된다.                                                      |
| Topic Exchagne   | Exchagne에 바인딩 된 Queue 중에서 메시지의 라우팅 키가 패턴에 맞는 Queue에세 모두 메시지를 전달한다. Mulicast 방섹에 접합하다.                                                     |
| Headers Exchagne | 라우팅 키 대신에 메시지 헤더에 여러 속성들을 더해 속성들이 매칭되는 큐에 메시지를 전달한다.                                                                                      |


## Rabbit MQ
RabbitMQ는 AMQP를 구현한 오픈소스 메시지 소프트웨어 Pulisher(Producer)로부터 메시지를 받아 Cunsumer에게 라우트하는 것이 주된 역할이다.

> 출처 [RabibitMQ Tutorials]([http://blog.naver.com/PostView.nhn?blogId=tmondev&logNo=220419853534&parentCategoryNo=&categoryNo=6&viewDate=&isShowPopularPosts=false&from=postView](http://www.rabbitmq.com/getstarted.htm))


![](/assets/rabbitmq-turorial.png)

### Rabbit MQ Exchagne Type
이름              | RabbitMQ 이름
----------------|---------------------------------------
Direct Exchange | (Empty string) and amq.direct
Fanout Exchange | anq.fanout
Topic Exchange  | amq.topic
Header Exchange | amq.match(and amq.headers in RabbitMQ)

# Sample Code

> 전체 코드는 [Blog-Sample Code](https://github.com/cheese10yun/blog-sample/tree/master/rabbitmq-sample)를 참고하세요

```java
public interface RabbitMqEvent {
  String MEMBER_SIGNUPED_EVENT = "signuped.event";
}
```
* 이벤트 이름을 작성하는 객체입니다. 인터페이스로 관리하는게 효율적으로 보여짐


## Publisher : RabbitTemplate
```java
  @Bean
  public RabbitTemplate amqpTemplate() {
    RabbitTemplate rabbitTemplate = new RabbitTemplate();
    rabbitTemplate.setConnectionFactory(connectionFactory);
    rabbitTemplate.setMandatory(true);
    rabbitTemplate.setChannelTransacted(true);
    rabbitTemplate.setReplyTimeout(60000);
    rabbitTemplate.setMessageConverter(queueMessageConverter());
    return rabbitTemplate;
  }
```
* 이벤트를 Publisher하는 template를 생성합니다.
* ConnectionFactory 의존성 주입을 받아 생성합니다.
* message conveter를 등록시킵니다. 다른 예제들은 Bean으로 등록시는데 딱히 Bean으로 등록시킬 필요는 없어 보입니다.
* `setChannelTransacted` 플래극 값은 Transaction 으로 생성하게 해줍니다. (`@Transactional` 이 붙은 것처럼 처리됩니다. 만약 데이터베이스의 트랜잭션과 연결된다면 트랜잭션이 전이 된다고 이해했습니다.)
* `setReplyTimeout` amqpTemplate의 timeout을 지정합니다.



## Comsumer : SimpleRabbitListenerContainerFactory

> [spring-amqp Document](https://docs.spring.io/spring-amqp/api/org/springframework/amqp/rabbit/listener/AbstractMessageListenerContainer.html#setDefaultRequeueRejected-boolean-)을 참고했습니다. 제가 이해한 부분으로 설명하는 것이라 틀린 부분이 있을 수 있습니다.

```java
@Bean
  public SimpleRabbitListenerContainerFactory rabbitListenerContainerFactory(
      ConnectionFactory connectionFactory) {
    final SimpleRabbitListenerContainerFactory factory = new SimpleRabbitListenerContainerFactory();
    factory.setConnectionFactory(connectionFactory);
    factory.setDefaultRequeueRejected(false);
    factory.setMessageConverter(queueMessageConverter());
    factory.setChannelTransacted(true);
    factory.setAdviceChain(RetryInterceptorBuilder
        .stateless()
        .maxAttempts(MAX_TRY_COUNT)
        .recoverer(new RabbitMqExceptionHandler())
        .backOffOptions(INITIAL_INTERVAL, MULTIPLIER, MAX_INTERVAL)
        .build());
    return factory;
  }
```
* `setDefaultRequeueRejected` true일 경우 리서너에서 예외가 발생시에 다시 큐에 쌓이게 됩니다. 예외 상황을 해제하기 전까지 무한 반복할 거같아 기본 설정이 false이고 true 변경할 일이 거의 없어 보입니다.
* `setChannelTransacted` 위 설정과 동일합니다.
* `recoverer` 예외를 핸들링할 수 있는 객체를 지정해서 핸들링 할 수 있습니다. 에러 로그, 에러 알림 정도 설정하면 좋을거 같음
* `maxAttempts` 예외가 발생 했을 경우 몇번을 더 재시도 할 횟수를 지정합니다.
* `backOffOptions` 재시도 횟수에 대한 옵션을 지정합니다. `3000, 3, 10000` 인자값을 지정 했다면 3초 간격으로 3으로 곱해서 최대 10초 까지 재시도 하라는 것입니다.

```java
public Member doSignUp(final SignUpRequest dto) {
    final Member member = memberRepository.save(dto.toEnttiy());
    amqpTemplate.convertAndSend(RabbitMqEvent.MEMBER_SIGNUPED_EVENT, SignUpedEvent.of(member));
    return member;
  }
```
* `convertAndSend` 위에서 등록한 template 기반으로 이벤트를 발생시킵니다.
* 첫 번째 인자는 이벤트의 이름(스트링 벨류), 이벤트 처리시에 넘겨줄 객체를 바인딩 시킵니다.

```java
  @RabbitListener(queues = RabbitMqEvent.MEMBER_SIGNUPED_EVENT)
  public void handleSignUpEvent(final SignUpedEvent event) {
    log.error(event.toString());
    throw new IllegalArgumentException();
  }
```
* `@RabbitListener` 어노테이션으로 리슨할 이벤트 이름을 지정합니다.
* `convertAndSend`에서 넘겨준 객체 `SignUpedEvent`를 매게변수를 받습니다.
* `IllegalArgumentException();` 설정을 통해서 예외를 발생시킵니다. 위에서 설정한 3번의 일정한 반복이 진행되는지 확인 해 봅니다.

![](/assets/rabbitmq-retry.png)


```
2019-03-03 20:43:57.459 ERROR 58190 --- [cTaskExecutor-1] y.b.rabbitmqsample.event.SignUpListener  : SignUpedEvent(id=1, email=asdasd@asdsad.com, name=asdasd)
2019-03-03 20:44:00.466 ERROR 58190 --- [cTaskExecutor-1] y.b.rabbitmqsample.event.SignUpListener  : SignUpedEvent(id=1, email=asdasd@asdsad.com, name=asdasd)
2019-03-03 20:44:09.470 ERROR 58190 --- [cTaskExecutor-1] y.b.rabbitmqsample.event.SignUpListener  : SignUpedEvent(id=1, email=asdasd@asdsad.com, name=asdasd)

...

 Retries exhausted for message (Body:'{"id":1,"email":"asdasd@asdsad.com","name":"asdasd"}' MessageProperties [headers={__TypeId__=yun.blog.rabbitmqsample.event.SignUpedEvent}, contentType=application/json, contentEncoding=UTF-8, contentLength=0, receivedDeliveryMode=PERSISTENT, priority=0, redelivered=false, receivedExchange=, receivedRoutingKey=signuped.event, deliveryTag=1, consumerTag=amq.ctag-XBOTLdkMvnhT6vypyy7HTQ, consumerQueue=signuped.event])
```

* 3번의 재시도가 시도됨
* 등록된 예외 핸들러 `RabbitMqExceptionHandler` 객체에서 로깅 출력