![EC2 Innound](http://i.imgur.com/2oTgfVc.png)

이전에 [Passport 간단로그인](https://cheese10yun.github.io/2017/01/01/Passport-part1/) [Passport 데이터베이스 연동](https://cheese10yun.github.io/2017/01/05/passport-mysql/)을 포스팅했습니다. 그래서 이번에는 Passport를 이용한 소셜 로그인 **페이스북, 카카오, 네이버 로그인 및 회원 가입** 기능을 포스팅해보겠습니다. Passport에 대해서 기초지식이 없으시며 앞서 포스티한 주제를 보시는 것을 권장드립니다. 또한 [GitHub develop-passport-social-login](https://github.com/cheese10yun/node-yun)에 올려져있는 전체 소스를 보는 것을 권장드립니다.

### Developers 등록
![EC2 Innound](http://i.imgur.com/o57zRqX.png)


본 포스팅에서는 개발자 등록은 다루지 않겠습니다. 아래의 작업을 하기위해서는 네이버, 카카오, 페이스북 개발자 센터에서 앱설정을 완료해주세요. 본 포스팅은 Passport 로직에 집중하겠습니다.


### 필수 모듈설치 및 require


```
npm install passport-facebook --save
npm install passport-kakao --save
npm install passport-naver --save
```


`index.js(router)`설치한 모듈을 require 시켜줍니다.

```javascript
var NaverStrategy = require('passport-naver').Strategy;
var FacebookStrategy = require('passport-facebook').Strategy;
var KakaoStrategy = require('passport-kakao').Strategy;
```

### Developers 정보 등록

더이상 데이터베이스 정보만 갖고 있지 않아 `db_info.js` 에서 `secret.js ` 변경했습니다. `db_info` 로 데이터베이스 접근을 진행하겠습니다. 자세한 내용은 아래에서 다시 설명드리겠습니다.

```javascript
module.exports = {
  'secret' :  '',
  'db_info': {
    local: { // localhost
    ...
    },
    real: { // real
    ...
    },
    dev: { // dev
    ...
    }
  },
  'federation' : {
    'naver' : {
      'client_id' : '',
      'secret_id' : '',
      'callback_url' : '/auth/login/naver/callback'
    },
    'facebook' : {
      'client_id' : '',
      'secret_id' : '',
      'callback_url' : '/auth/login/facebook/callback'
    },
    'kakao' : {
      'client_id' : '',
      'callback_url' : '/auth/login/kakao/callback'
    }
  }
};
```

`federation` 객체에 네이버, 카카오, 페이스북 Developers 정보를 입력합니다.  `secret.js`  민감한 정보를 갖고있는 파일들은 프로젝트 디렉토리에 포함시키지 않는 것이 바람직합니다. 깃허브에는 소스코드를 공개하기 위해서 업로드하긴 했습니다...~~안티패턴도 좋은 경험 입니다.~~

### 회원 가입 페이지

`login.hbs <a href>` 태그에 로그인 처리를 담당할 URL을 입력합니다. 자세한 로직은 아래에서 설명하겠습니다.

```html
<form action="/login" method="post" name="frm_login" id="frm_login">
  <div class="modal-dialog">
	  <div class="modal-content">
      <div class="modal-header">
          <h1 class="text-center">Node JS Passport Login</h1>
      </div>
      <div class="modal-body">
        <div class="form-group">
            <input type="text" id="username" name="username" class="form-control input-lg" placeholder="ID" required="required" autocomplete="off" />
        </div>

        <div class="errorTxt"></div>
        <div class="form-group">
            <input type="password" id="password" name="password" class="form-control input-lg" placeholder="Password" required="required" autocomplete="off" />
        </div>
        <div class="errorTxt"></div>
        <div class="form-group">
            <input type="submit" class="btn btn-block btn-lg btn-default btn_login" value="Login" />
            <a href="/auth/login/facebook" class="btn btn-block btn-lg btn-primary btn_login">FaceBook</a>
            <a href="/auth/login/kakao" class="btn btn-block btn-lg btn-warning btn_login">KaKao</a>
            <a href="/auth/login/naver" class="btn btn-block btn-lg btn-success btn_login">Naver</a>
        </div>
      </div>
    </div>
  </div>
</form>
```

### Passport 로직 구현

`index.js(router)`**네이버 로그인**

```javascript
var secret_config = require('../commons/secret');

passport.use(new NaverStrategy({
    clientID: secret_config.federation.naver.client_id,
    clientSecret: secret_config.federation.naver.secret_id,
    callbackURL: secret_config.federation.naver.callback_url
  },
  function (accessToken, refreshToken, profile, done) {
    var _profile = profile._json;

    loginByThirdparty({
      'auth_type': 'naver',
      'auth_id': _profile.id,
      'auth_name': _profile.nickname,
      'auth_email': _profile.email
    }, done);
  }
));
```

`index.js(router)`**카카오 로그인**
```javascript
passport.use(new KakaoStrategy({
    clientID: secret_config.federation.kakao.client_id,
    callbackURL: secret_config.federation.kakao.callback_url
  },
  function (accessToken, refreshToken, profile, done) {
    var _profile = profile._json;

    loginByThirdparty({
      'auth_type': 'kakao',
      'auth_id': _profile.id,
      'auth_name': _profile.properties.nickname,
      'auth_email': _profile.id
    }, done);
  }
));
```

`index.js(router)` **페이스북 로그인**

```javascript
passport.use(new FacebookStrategy({
    clientID: secret_config.federation.facebook.client_id,
    clientSecret: secret_config.federation.facebook.secret_id,
    callbackURL: secret_config.federation.facebook.callback_url,
    profileFields: ['id', 'email', 'gender', 'link', 'locale', 'name', 'timezone',
      'updated_time', 'verified', 'displayName']
  }, function (accessToken, refreshToken, profile, done) {
    var _profile = profile._json;

    loginByThirdparty({
      'auth_type': 'facebook',
      'auth_id': _profile.id,
      'auth_name': _profile.name,
      'auth_email': _profile.id
    }, done);
  }
));
```


* accessToken : OAtuh tokken을 이용해서 네이버, 카카오, 페이스북 오픈 API를 호출합니다.
* refreshToken : tokken이 만료되었을 때 재발급을 용청합니다.
* profile : 네이버, 카카오, 페이스북의 사용자의 정보가 들어있습니다. (이걸 얻기위해서...이 많은 소스를...)


로직은 크게 다르지 않아 한 번에 설명하겠습니다. `secret.js` `federation` 객체있는 `client_id, secret_id, callback_url`를  passport 에 바인딩 시킵니다.  profile 파라미터로 네이버, 카카오, 페이스북 프로필 정보가 각기 다르게 들어오기 때문에 공통이름으로 바인딩 시키고 `loginByThirdparty()` 메서드에 바인딩 시킨 유저 정보와 done을 넘겨 줍니다. `loginByThirdparty()` 메서드에서 로그인 처리 및 회원 가입 절차가 진행되게 됩니다. 아래에서 자세하게 설명하겠습니다.

`index.js(router)`라우처 처리

```javascript
// naver 로그인
router.get('/auth/login/naver',
  passport.authenticate('naver')
);
// naver 로그인 연동 콜백
router.get('/auth/login/naver/callback',
  passport.authenticate('naver', {
    successRedirect: '/',
    failureRedirect: '/login'
  })
);

// kakao 로그인
router.get('/auth/login/kakao',
  passport.authenticate('kakao')
);
// kakao 로그인 연동 콜백
router.get('/auth/login/kakao/callback',
  passport.authenticate('kakao', {
    successRedirect: '/',
    failureRedirect: '/login'
  })
);

// facebook 로그인
router.get('/auth/login/facebook',
  passport.authenticate('facebook')
);
// facebook 로그인 연동 콜백
router.get('/auth/login/facebook/callback',
  passport.authenticate('facebook', {
    successRedirect: '/',
    failureRedirect: '/login'
  })
);
```

반복적인 소스라 대표적으로 **Naver** 기준으로 통합해서 설명하겠습니다. `login.hbs` 에서 네이버 로그인을 클릭했을 경우 `router.get('/auth/login/naver'...)` 으로 요청이들어고 아래의 `NaverStrategy` 모듈로 다시 전달됩니다.

`passport.use(new NaverStrategy(...)` 여기에서는 OAuth 요청을 만들어서 네이버 로그인 처리를 진행하고 요청결과를 아래의 라우터로 리다이렉트를 시켜줍니다.

`router.get('/auth/login/naver/callback'...)` 해당 라우터는 로그인이 성공할 경우 인덱스 페이지로 리다이렉트 시켜주고 실패할 경우에는 다시 `'/login'` 다시 로그인 페이지로 리다이렉트 시켜줍니다.

### 로그인 및 회원 가입 로직

```javascript
function loginByThirdparty(info, done) {
  console.log('process : ' + info.auth_type);
  var stmt_duplicated = 'select *from `user` where `user_id` = ?';

  connection.query(stmt_duplicated, info.auth_id, function (err, result) {
    if (err) {
      return done(err);
    } else {
      if (result.length === 0) {
        // 신규 유저는 회원 가입 이후 로그인 처리
        var stmt_thridparty_signup = 'insert into `user` set `user_id`= ?, `nickname`= ?';
        connection.query(stmt_thridparty_signup, [info.auth_id, info.auth_name], function (err, result) {
          if(err){
            return done(err);
          }else{
            done(null, {
              'user_id': info.auth_id,
              'nickname': info.auth_name
            });
          }
        });
      } else {
        //기존유저 로그인 처리
        console.log('Old User');
        done(null, {
          'user_id': result[0].user_id,
          'nickname': result[0].nickname
        });
      }
    }
  });
}
```

`auth.id` 기반으로 신규 회원인지 기존 회원인지 판단합니다. 신규 회원일 경우에는 `user` 테이블에 회원 정보를 저장 시키고 로그인 처리를 진행하고, 기존 유저일 경우에는 쿼리로 조회한 회원정보를 기반으로 로그인 처리를 진행합니다.
간단하게 말씀드리면 신규회원이든 기존 회원이든 원클릭으로 회원 가입 절차를 진행하게 할 수 있습니다.

사실 위의 예제는 좀 과격?한 부분이 좀있습니다. `auth.id` 는 중복될 일이 거의 없을 거 같긴 하지만  `auth.nickname` 같은 경우는 중복의 여지가 상당합니다. 사실 위의 예제는 중복의 여지가 있으니 추후 로직은 각자 알아서....~~안티패턴도 좋은 경험 입니다.~~

저희 서비스에는 타 게임회사의 계정과 연동해야 하기 때문에 소셜 회원가입을 진행한 사용자에 대해서는 게임 로그인 진행 전에 아이디, 패스워드, 닉네임값을 한 번 더 입력 더 받고 있습니다.

교보문고 사이트 경우에도 네이버 아이디로 회원가입을 진행하고 교보문고 계정에 대해서 아이디, 패스워드를 한 번 더 입력받는 시스템입니다. ~~우리만 그런게 아니야...~~

### 마무리
위의 예제는 깃허브에 있는 전체 코드를 보시는 것을 권장해 드립니다. 생각보다 예제소스가 길어서 설명하는 게 매끄럽지 못한 거 같습니다. 로그인 및 회원 가입 로직은 각자 환경에 맞게 설계해야 할 거 같아 최대한 단순하게 로직 처리를 진행하게 됐습니다. 긴글 읽어주셔서 감사합니다.
