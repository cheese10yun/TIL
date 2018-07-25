# Slack Notification
Spring boot으로 초간단하게 Slack Message 보내기를 만들어 보는 프로젝트입니다. [Slack Message](https://api.slack.com/docs/messages/builder) 에서 제공하는 3가지 메시지를 보내 보겠습니다. 전체 코드는 [GitHub](https://github.com/cheese10yun/spring-boot-slack-notificaion) 보시는 것을 권장해 드립니다.

# Slack Web Hook 연동
![](https://i.imgur.com/MBhHN58.png)

Incoming WebHooks 작업이 완료 됬다고 가정하고 포스팅을 진행하겠습니다. 만약 작업이 완료 되지 않았다면 해당 앱을 설치하고 특정 채널에 알림이 가게 설정해주세요

# Slack Message 유형

크게 3 가지 유형이 있습니다. 이미지 형태와 컨트롤러에서 보내 볼 수 받아 볼 수 있는 Curl 하나하나 살펴보습니다.

## Basic

### 이미지
![](https://i.imgur.com/W6Gg0eV.png)

```bash
curl -X POST \
  http://localhost:8080/slack/basic \
  -H 'cache-control: no-cache' \
  -H 'content-type: application/json' \
  -H 'postman-token: db7316c5-3358-0ccc-4de4-03bd7dd307c4' \
  -d '{
	"text": "text"
}'
```

## Attachments

### 이미지
![](https://i.imgur.com/Povsbzl.png)

### Curl
```bash
curl -X POST \
  http://localhost:8080/slack/button \
  -H 'cache-control: no-cache' \
  -H 'content-type: application/json' \
  -H 'postman-token: 0b503f02-713c-afdc-8c8f-b007c7bfb882' \
  -d '{
    "text": "Would you like to play a game?",
    "attachments": [
        {
            "text": "Choose a game to play",
            "fallback": "You are unable to choose a game",
            "callback_id": "wopr_game",
            "color": "#3AA3E3",
            "attachment_type": "default",
            "actions": [
                {
                    "name": "game",
                    "text": "Chess",
                    "type": "button",
                    "value": "chess"
                },
                {
                    "name": "game",
                    "text": "Falken'\''s Maze",
                    "type": "button",
                    "value": "maze"
                },
                {
                    "name": "game",
                    "text": "Thermonuclear War",
                    "style": "danger",
                    "type": "button",
                    "value": "war",
                    "confirm": {
                        "title": "Are you sure?",
                        "text": "Wouldn'\''t you prefer a good game of chess?",
                        "ok_text": "Yes",
                        "dismiss_text": "No"
                    }
                }
            ]
        }
    ]
}'
```

## Message buttons

### 이미지
![](https://i.imgur.com/GEAfeAJ.png)

### Curl
```bash
curl -X POST \
  http://localhost:8080/slack/attachment \
  -H 'cache-control: no-cache' \
  -H 'content-type: application/json' \
  -H 'postman-token: 53d665f3-c1e7-064d-e957-525a703e3965' \
  -d '{
  "attachments": [
    {
      "fallback": "Required plain-text summary of the attachment.",
      "color": "#36a64f",
      "pretext": "Optional text that appears above the attachment block",
      "author_name": "Bobby Tables",
      "author_link": "http://flickr.com/bobby/",
      "author_icon": "http://flickr.com/icons/bobby.jpg",
      "title": "Slack API Documentation",
      "title_link": "https://api.slack.com/",
      "text": "Optional text that appears within the attachment",
      "fields": [
        {
          "title": "Priority",
          "value": "High"
        }
      ],
      "image_url": "http://my-website.com/path/to/image.jpg",
      "thumb_url": "http://example.com/path/to/thumb.png",
      "footer": "Slack API",
      "footer_icon": "https://platform.slack-edge.com/img/default_application_icon.png",
      "ts": 1
    }
  ]
}'
```

# Slack Notificaion 만들기

## Web Hook 연결

```java
@Getter
public enum SlackTarget {
    CH_BOT("발급받은 Web Hook URL", "채널 이름");

    private final String webHookUrl;
    private final String channel;

    SlackTarget(String webHookUrl, String channel) {
        this.webHookUrl = webHookUrl;
        this.channel = channel;
    }
}
```

슬랙 Web Hook URL 을 입력해주세요

## DTO 만들기

Attachments JSON을 DTO로 만들어 보겠습니다.

```java
@Getter
@NoArgsConstructor
public static class Attachments {
    private List<Attachment> attachments;

    @Builder
    public Attachments(List<Attachment> attachments) {
        this.attachments = attachments;
    }
}

@Getter
@NoArgsConstructor
public static class Attachment {
    private String fallback;
    private String color;
    private String pretext;
    @JsonProperty("author_name") private String authorName;
    @JsonProperty("author_link") private String author_link;
    @JsonProperty("author_icon") private String authorIcon;
    private String title;
    @JsonProperty("title_link") private String titleLink;
    private String text;
    @JsonProperty("image_url") private String imageUrl;
    @JsonProperty("thumb_url") private String thumbUrl;
    private String footer;
    @JsonProperty("footer_icon") private String footerIcon;
    private Long ts;
    private List<Field> fields;
}
```

## Controller

```java
@RequestMapping(value = "attachment", method = POST)
public void attachment(@RequestBody SlackMessageDto.Attachments dto) {
    slackSenderManager.send(SlackTarget.CH_BOT, dto);
}
```

## Slack Web Hook 보내기

```java
public void send(SlackTarget target, Object object) {
    restTemplate.postForEntity(target.getWebHookUrl(), writeValueAsString(object), String.class);
}
```
**restTemplate 을 이용해서 컨트롤러에서 받은 DTO를 JSON으로 보내주고 있습니다.**


