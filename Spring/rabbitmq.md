# 스프링 부트 2.0 마이크로서비스와 리액티브 프로그래밍 정리
> 출저 [스프링 부트 2.0 마이크로서비스와 리액티브 프로그래밍](http://www.kyobobook.co.kr/product/detailViewKor.laf?ejkGb=KOR&mallGb=KOR&barcode=9791161752624&orderClick=LAA&Kc=)을 보고 정리한 자료입니다.

## Rabbmit MQ란 ?
래빗MQ는 오픈소스 AMQP 브로커다. AMQP는 유선을 통해 전송되는 메시지 형식을 포함하는 개발형 프로토컬이며, JMS와 같은 다른 메시징 솔루션에 비해 인기가 높다.

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

> 출저 [RabbitMQ와 Spring AMQP를 이용한 간단한 작업 큐 만들기](http://blog.naver.com/PostView.nhn?blogId=tmondev&logNo=220419853534&parentCategoryNo=&categoryNo=6&viewDate=&isShowPopularPosts=false&from=postView)를 보고 정리한 내용입니다.


## AMQP
프로그래밍에서 MQ는 프로세스 또는 프로그램 인스턴스가 데이터를 서로 교환할 때 사용하는 방법을 말한다. 이러한 MQ를 오픈소스에 기반한 표준 프로토콜이 AMQP이다. AMQP 자체가 프로토콜을 의미하기 때문에 이 프로토콜을 구현한 MQ 제품들은 여러가지가 있으며 그 중하나가 RabbitMQ이다.

### AMQP의 구성요소와 라우팅 알고리즘
![](/draw/rabbitmq.png)

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

> 출저 [RabibitMQ Tutorials]([http://blog.naver.com/PostView.nhn?blogId=tmondev&logNo=220419853534&parentCategoryNo=&categoryNo=6&viewDate=&isShowPopularPosts=false&from=postView](http://www.rabbitmq.com/getstarted.htm))


![](/draw/rabbitmq-turorial.png)

### Rabbit MQ Exchagne Type
이름              | RabbitMQ 이름
----------------|---------------------------------------
Direct Exchange | (Empty string) and amq.direct
Fanout Exchange | anq.fanout
Topic Exchange  | amq.topic
Header Exchange | amq.match(and amq.headers in RabbitMQ)