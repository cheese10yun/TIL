# 카카오 봇만들기

## 카카오 봇 API

### keyboad API

```
Method : GET
URL : http(s)://:your_server_url/keyboard
Content-Type : application/json; charset=utf-8
```

#### 요청
```
curl -XGET 'https://:your_server_url/keyboard'
```
### 응답
```
"type" : "buttons",
"buttons" : ["선택 1", "선택 2", "선택 3"]
```

### Message API

```
Method : POST
URL : http(s)://:your_server_url/message
Content-Type : application/json; charset=utf-8
Parameters
user_key	String	Required	메시지를 발송한 유저 식별 키
type	String	Required	text, photo
content	String	Required	자동응답 명령어의 메시지 텍스트 혹은 미디어 파일 uri
```
#### 요청
```
curl -XPOST 'https://:your_server_url/message' -d '{
     "user_key": "encryptedUserKey",
     "type": "text",
     "content": "차량번호등록"
 }'

 curl -XPOST 'https://your_server_url/message' -d '{
     "user_key": "encryptedUserKey",
     "type": "photo",
     "content": "http://photo_url/number.jpg"
 }'
```
#### 응답
```
{
    "message":{
    "text" : "귀하의 차량이 성공적으로 등록되었습니다. 축하합니다!"
    }
 }

 Response ex )

 {
     "message": {
         "text": "귀하의 차량이 성공적으로 등록되었습니다. 축하합니다!",
         "photo": {
         "url": "https://photo.src",
         "width": 640,
         "height": 480
     },
     "message_button": {
         "label": "주유 쿠폰받기",
         "url": "https://coupon/url"
        }
     },
     "keyboard": {
         "type": "buttons",
         "buttons": [
            "처음으로",
            "다시 등록하기",
            "취소하기"
         ]
     }
 }
```
