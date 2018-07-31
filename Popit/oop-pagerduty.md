최근 외부 API를 통신하는 일들이 많이 있었고 최근 PagerDuty API 작업을 하면서 외부 API를 사용할 때 많은 시행착오와 고민을 나름 정리해 보았습니다.

# PagerDuty Request
```json
{
    "routing_key": "routing_key...",
    "event_action": "only trigger, acknowledge, resolve",
    "images": [],
    "links": [],
    "payload": {
        "summary": "summary...(string)",
        "source": "System...",
        "severity": "only critical, error, warning, info"
    }
}
```

# PagerDuty Response
```json
{
  "status": "success",
  "message": "Event processed",
  "dedup_key": "samplekeyhere"
}
```

[PagerDuty Document](https://v2.developer.pagerduty.com/docs/send-an-event-events-api-v2)에 나와 있는 Request, Response를 JSON으로 정리했습니다. Request 값은 필수 값 위주로 정리했습니다.


# Request, Response Class

```java
@Getter
public static class Request {
    @JsonProperty("event_action")
    private final EventAction eventAction;
    @JsonProperty("routing_key")
    private final String routingKey = "routingKey...";
    private final Payload payload;

    @Builder
    public Request(final EventAction eventAction, final Payload payload) {
        this.eventAction = eventAction;
        this.payload = payload;
    }
}

@Getter
public static class Payload {
    private final String summary;
    private final String timestamp = ZonedDateTime.now().toOffsetDateTime().format(DateTimeFormatter.ISO_OFFSET_DATE_TIME);
    private final Severity severity;
    private final Group group;
    private final Source source;
    @JsonProperty("custom_details")
    private final Object customDetails;

    @Builder
    public Payload(final String summary, final Severity severity, final Group group, final Source source, final Object customDetails) {
        this.summary = summary;
        this.severity = severity;
        this.group = group;
        this.source = source;
        this.customDetails = customDetails;
    }
}

@Getter
public static class Response {
    private String status;
    private String message;
    @JsonProperty("dedup_key")
    private String dedupKey;

}
```

## Request, Response에 대한 DTO 클래스 생성
위의 JSON을 클래스로 바인딩시킬 DTO 클래스를 생성해서 API에 대한 Request, Response를 관리하는 것이 바람직합니다. 간혹 Map, JSON(gsoon, jackson) 등을 이용해서 유연하게 두는 예도 있지만 **저는 개인적으로 권장하지 않습니다.** 가장 큰 이유는 해당 값에 무슨 데이터가 있는지 확인하기 어렵습니다. 코드 가독성이 심각하게 떨어지며 정확히 어떤 자료형인지 확인하기도 어렵습니다. 또 @JsonProperty를 통해서 해당 실제 JSON 키값과 객체의 멤버 필드 값을 다르게 설정 할 수 있습니다.

## Setter를 사용하지 않기
이전 포스팅에서도 [Setter 사용하지 않기](https://github.com/cheese10yun/spring-jpa-best-practices/blob/master/doc/step-06.md)를 언급한 적이 있습니다. Response DTO 클래스 같은 경우는 더욱 Setter를 제공할 필요가 없지만, 관습적으로 Setter 메서드를 추가하는 경우가 많습니다. **해당 객체를 어디서든지 변경이 가능한 객체가 되기 때문에 명확한 이유 없이 관습적인 Setter는 반드시 지양 해야 합니다.**

반면 Request 객체는 필요한 값을 바인딩시켜 보내줘야 합니다. 이러한 경우는 Builder 패턴을 이용해서 객체를 생성하면 많은 장점이 있습니다. 제가 느끼는 대표적인 장점들 아래와 같습니다.

```java
PagerDutyDto.Request.builder()
    .eventAction(PagerDutyDto.EventAction.acknowledge)
    .payload(Payload ...)
    .build();
```

1. 인자의 순서에 영향 받지 않습니다. 생성자로 생성했을 경우 인자의 순서가 중요하며, 특히 같은 자료형에 대한 인자는 실수할 수 있는 여지가 높습니다.
2. **Setter 메서드가 없어 변경 불가능 객체를 만들 수 있습니다.**

그렇다면 객체의 수정이 필요 하면 어떻게 어떻게 해야 할까요?

```java
class User {
    private String email;
    private String name;

    public void changeEmail(String email) {
        this.email = email;
    }
}
```
객체의 멤버필드를 수정 할 수 있는 수정 메소드를 재공 해주면 됩니다. 단순하게 setter라는 네이밍으로 공개하는 것보다 해당 메소드의 기능을 잘 표현하는 네이밍을 갖는 것이 적합합니다. 더 구체적인 내용은 이전 포스팅 [Settet 사용하지 않기](https://github.com/cheese10yun/spring-jpa-best-practices/blob/master/doc/step-06.md#updatemyaccount-%EB%A9%94%EC%84%9C%EB%93%9C%EB%A5%BC-%EC%9D%B4%EC%9A%A9%ED%95%9C-%EC%97%85%EB%8D%B0%EC%9D%B4%ED%8A%B8)에 정리 되어 있습니다.


# Enum을 활용한 안전성 보장
```json
{
    "event_action": "only trigger, acknowledge, resolve",
    "payload": {
        "severity": "only critical, error, warning, info"
    }
}
```
PagerDuty API event_action, payload.severity 의 value는 위에서 정의한 값 이외의 값이 넘어오면 status 400이 리턴 됩니다. 이러한 값들에 대해서는 아래 코드처럼 enum 클래스로 안전성을 확보하는 것이 바람직합니다.

```java
public enum EventAction {
    trigger, acknowledge, resolve
}
public enum Severity {
    info, error, warn
}
```
String으로 관리할 경우 해당 값 이외의 입력에 대한 예외코드를 작성하면 런타임시에 발생하게 됩니다. enum 클래스로 관리했을 경우 개발자는 해당 enum 값이 외에는 값을 넣을 수도 없게끔 강제하는 것이 실수를 줄이는 방법입니다. String은 변경 및 유지 보수에 취약합니다. 이처럼 API에서 강제된 항목들은 enum 클래스로 관리하는 것이 바람직합니다.

# 각각의 메소드에 책임 부여

```java
public void sendErrorMessage() {
    // 적절한 Error Message를 가공하는 책임
    // 해당 메소드의 변경시 이유는 Error Message의 변경시에만 변경 이유를 갖는다
    final PagerDutyDto.Request request = buildErrorMessage();
    send(request);
}

public void sendInfoMessage() {
    // 적절한 Info Message를 가공하는 책임
    // 해당 메소드의 변경시 이유는 Info Message의 변경시에만 변경 이유를 갖는다
    final PagerDutyDto.Request request = buildInfoMessage();
    send(request);
}

private PagerDutyDto.Response send(final PagerDutyDto.Request request) {
    // 넘겨 받은 request를 PagerDuty 에게 보내는 책임
    // 해당 메소드의 변경시 이유는 PagerDuty API 변경시만 변경 이유를 갖는다. -> Request, Response 변경이 대표적인 예
    try {
        final String url = "https://events.pagerduty.com/v2/enqueue";
        return restTemplate.postForObject(url, request, PagerDutyDto.Response.class);
    } catch (HttpClientErrorException e) {
        log.error(e.getResponseBodyAsString(), e);
        throw new RuntimeException(e);
    }
}
```

>클린 코드 출처
>
>**작게 만들어라!**
>
>함수를 만드는 첫 번째 규칙은 '작게!'다. 함수를 만드는 둘째 규칙은 '더 작게!'다. ...중략
>
>**한 가지만 해라!**
>
>함수는 한 가지를 해야 한다. 그 한 가지를 잘해야 한다. 그 한 가지만을 해야 한다. ... 중략

**클린 코드에서 언급했듯이 함수의 기본단위는 정말 작아야 한다고 생각합니다. 그래야 재사용성이 높아지며 가독성, 유지보수에 좋은 코드가 된다고 생각합니다.**

sendErrorMessage() 메소드는 적절하게 Error Message를 만들어서 send 메소드에게 전달해주는 것이 그 함수가 하는 일입니다. 함수의 크기가 작으며 적은 일을 수행하고 있습니다.

send() 메소드도 넘겨받은 request값을 PagerDutry API 에게 요청하고 그에 따른 응답값을 받는 일만 합니다. 그렇게 된 결과 sendInfoMessage() 메서드에서도 재사용성이 높아지고, 해당 함수가 하는 일이 단순해져서 가독성이 높아집니다. 또 위에서도 언급했듯이 PagerDuty API 변경시에만 해당 메소드가 변경됩니다. 그런 결과 유지 보수하기 편한 이점이 있다고 생각합니다.




