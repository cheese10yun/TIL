#

## 관련 없는 모델에대한 참조 방법

```javascript
module.exports = function(MyModel) {
  var app = require('../../server/server');

  MyModel.observe('loaded', function( ctx, next) {
   var User = app.models.User;
   ...
   User.create(...);
   ...
});
```

* `server.js`를 참조해서 다른 모델에 대한 접근 가능

## 부트스크립트
### 비동기 부팅 스크립트 - /server/boot/your-script.js
```javascript
module.exports = function(app, cb) { //app is injected by LoopBack
  //...
};
```
### 콜백 기능이없는 동기식 부트 스크립트 : 동기 부트 스크립트 - /server/boot/your-script.js

```javascript
module.exports = function(app) { //app is injected by loopback
  //...
};
```

* 두 예제 모두에서 볼 수 있듯이 LoopBack은 app 부팅 스크립트의 첫 번째 매개 변수로 개체를 자동으로 제공합니다 .

## 미들웨어
* LoopBack은 app미들웨어의 request객체에 객체를 자동으로 설정  합니다 (실제로는 내부적으로 Express가 수행합니다). server/server.js다음과 같이 액세스 할 수 있습니다

```javascript
app.use(function(req, res, next) {
  var app = req.app;
  //...
});
```

## API 추가

## Error

```javascript
var err = new Error(g.f('Email is required'));
 err.statusCode = 400;
 err.code = 'EMAIL_REQUIRED';
```
* 에러코드 만들기
2
## Swagger API 문서 편집

```
"methods": {
    "existsToEmail": {
      "description": "이메일 중복검사",
      "notes": [
        "# 이메일 중복검사",
        "## Request",
        "* /users/existence/email?email=yun@test.com\r\n",
        "## Response",
        "### success",
        "* {exists: boolean} ",
        "* {statusCode: 200} "
      ],
      ..
      ..
      ..
```

* `notes` 객체에 마크다운 형식으로 작성 가능

## Validation

```javascript
var isEmail = require('isemail');
if (!isEmail.validate(value))
  return err('email');
```
* 이메일 유효성 검사


##
* 마이그레이션 작업 고민
* API URL 규칙 지키기 / 설계
* SMS 가입 타입에 따라서
* 펫 특이사항 JSON 타입의 배열 타입으로 한다.
* 유저 테이블 마이그레이션 작업 목요일 / 칼럼 픽스 / 금요일 마이그레이션 작업 진행
