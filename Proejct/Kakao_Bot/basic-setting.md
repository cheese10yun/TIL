# 카카오 봇만들기

## 카카오 봇 API 간단설명

### 용어 설명

#### 수신 API

- 카카오톡 이용자가 플러스친구, 옐로아이디에게 보낸 메시지를 전달 받은 후 응답을 할 수 있는 API 입니다. http(s) restful api를 통하여 카카오 API 서버 -> 파트너 서버를 호출합니다.

#### app_key

- 플러스친구/옐로아이디에서 자동응답을 위한 앱 등록시 프로필별로 발급되는 고유 키 값입니다. 자동응답 기능만 이용하시는 경우 사용되지 않으며, 일부 app_secret을 통한 별도 인증이 필요한 일부 프로필에만 사용됩니다.

#### app_secret

- 인증을 위해 app_key와 조합하여 사용되는 키 값입니다. 자동응답 기능만 이용하시는 경우 사용되지 않으며, 일부 app_secret을 통한 별도 인증이 필요한 일부 프로필에만 사용됩니다.

#### user_key

- 특정 카카오톡 이용자를 구분하기 위한 key 입니다. 카카오에서는 이용자의 개인정보를 외부에 제공하지 않으므로, 외부 파트너사에서 카카오톡 이용자를 구분하기 위해서는 카카오로부터 API를 통해 user_key를 response로 받아야 합니다. user_key는 특정 카카오톡 이용자에 대해 프로필별로 각기 다르게 발급됩니다. 따라서 user_key는 해당 프로필에 대해서만 유효합니다. 카카오톡 이용자가 프로필을 차단했다가 다시 추가한 경우에는 user_key가 갱신되지 않으며, 이용자가 카카오톡 탈퇴 후 재가입한 경우 갱신됩니다.

### API 설정

URL                  | method | 설명
-------------------- | ------ | ------------------------------------------------------------------------
/keyboard            | GET    | 키보드 영역에 표현될 버튼에 대한 정보. 생략시 text 타입이 선택된다
/message             | POST   | 자동응답 명령어에 대한 응답 메시지의 내용
/friend              | POST   | 특정 카카오톡 이용자가 플러스친구/옐로아이디를 친구로 추가하거나 차단하는 경우 해당 정보를 파트너사 서버로 전달하는 API입니다.
/friend              | DELETE | 특정 카카오톡 이용자가 플러스친구/옐로아이디를 친구로 추가하거나 차단하는 경우 해당 정보를 파트너사 서버로 전달하는 API입니다.
/chat_room/:user_key | DELETE | 사용자가 채팅방 나가기를 해서 채팅방을 목록에서 삭제했을 경우 해당 정보를 파트

[출저 : 카카오톡 플러스친구/옐로아이디 API v. 2.0 개요](https://github.com/plusfriend/auto_reply)

## Node API 설정

### Node keyboard API

```javascript
router.get('/keyboard', (req, res) => {
  const menu = {
      type: 'buttons',
      buttons: ["메뉴2", "메뉴2", "메뉴3"]
  };

  res.set({
      'content-type': 'application/json'
  }).send(JSON.stringify(menu));
});
```

### Response

```
{
  "type": "buttons",
  "buttons": [
    "메뉴1",
    "메뉴2",
    "메뉴3"
  ]
}
```

### Node Message API

```javascript
router.post('/message', (req, res) => {
    const _obj = {
        user_key: req.body.user_key,
        type: req.body.type,
        content: req.body.content
    };
    let massage = {
        "message": {
            "text": '응답 메세지...'
        },
        "keyboard": {
            "type": "buttons",
            "buttons": [
                "메뉴1",
                "메뉴2",
                "메뉴3"
            ]
        }
    };
    res.set({
        'content-type': 'application/json'
    }).send(JSON.stringify(massage));
});
```

### Response

```
{
  "message": {
    "text": '응답 메세지...'
  },
  "keyboard": {
    "type": "buttons",
    "buttons": [
      "메뉴1",
      "메뉴2",
      "메뉴3"
    ]
  }
}
```

### Node friend API

```javascript
router.post('/friend', (req, res) => {
    const
        user_key = req.body.user_key;
    console.log(`${user_key}님이 쳇팅방에 참가했습니다.`);

    res.set({
        'content-type': 'application/json'
    }).send(JSON.stringify({success:true}));
});
```

### Node chat_room API

```javascript
router.delete('/chat_room/:user_key', (req, res) => {
    const user_key = req.params.user_key;

    console.log(`${user_key}님이 쳇팅방에서 나갔습니다.`);

    res.set({
        'content-type': 'application/json'
    }).send(JSON.stringify({success:true}));
});
```

## 옐로우 아이디 설정

### [옐로우 아이디 등록](https://yellowid.kakao.com)

![옐로우아이디 등록](http://i.imgur.com/68DXtkf.png)

![옐로우아이디 경고](http://i.imgur.com/J9bloey.png)

**해당 프로필은 하번 설정하면 변경이 불가능합니다.**

![](http://i.imgur.com/YDxMB6D.png)

**앱 등록 절차를 진행합니다.**

![API형 자동읍답 선택](http://i.imgur.com/NDfHewo.png)

**API형 자동읍답 설정하기 선택**

#### 앱등록시 반드시 keyboard API가 동작해야합니다.

![API TEST](http://i.imgur.com/JUFtFR8.png)

## 프로젝트 실행 방법 (Mac 기준)

### Redis 설치 및 실행

```1
$ wget http://download.redis.io/redis-stable.tar.gz
$ tar xvzf redis-stable.tar.gz
$ cd redis-stable
$ make
$ redis-server
```

### Node 설정 및 실행

```
$ [sudo] npm install pm2 -g
$ cd Node-Boot/
$ [sudo] npm install
$ cd bin/
$ pm2 start www --name <APP_NAME>
```
**PM2 설정은 [Yun Blog PM2](https://cheese10yun.github.io/PM2) 참고**

**Redis Node 사용법은 [Yun Wiki Redis](https://github.com/cheese10yun/Yun-Wiki/blob/master/Node/Redis.md) 참고**
