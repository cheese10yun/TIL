#TDD in Node.js

## Mocha

 * 모카는 테스트 코드를 돌려주는 테스트 러너
 * 테스트 수트: 테스트 환경으로 모카에서는 `describe()`으로 구현한다.
 * 테스트 케이스: 실제 테스트를 말하며 모카에서는 `it()`으로 구현한다.

## 특징
* `*.spec.js` 테스트 코드


## 예제

`utils.spec.js`
```javascript
const utils = require('./utils');
const assert = require('assert');

describe('util.js 모듈의 capitializse() 함수는', ()=>{
  it('문자열의 첫번째 문자를 대문자로 변경한다.', ()=>{
    const result = utils.capitializse('hello');
    assert.equal(result,'Hello');
  });
});
```

* 해당 문자열의 첫글자가 대문자로 변경이되는지 검사하는 테스트코드


`utils.js`
```javascript
function capitializse(str) {
  return str.charAt(0).toUpperCase() + str.slice(1);
}

module.exports = {
  capitializse: capitializse
};
```

* 문자열의 첫글자를 대문자료 변경하는 `capitializse` 함수 작성

## Should
* `assert` 보다는 `Should`로 데이터를 검증하는것이 바람직하다

## 예제
```javascript
const utils = require('./utils');
const should = require('should');

describe('util.js 모듈의 capitializse() 함수는', ()=>{
  it('문자열의 첫번째 문자를 대문자로 변경한다.', ()=>{
    const result = utils.capitializse('hello');
    // assert.equal(result,'Hello');
    result.should.be.equal('Hello');
  });
});
```
* 테스트 코드에 가독성을 높일수 있다.

## SuperTest
* 단위 테스트 : 함수의 기능 테스트
* 통합 테스트 : API 기능 테스트
* 슈퍼 테스트는 익스프레스 통합 테스트용 라이브러리다.
* 내부적으로 익스프레스 서버를 구동시켜 실제 요청을 보낸뒤 결과를 검증한다.

### 테스트 시나리오

#### 성공

* 유저 객체를 담은 배열로 응답한다.
* 최대 limit 갯수만큼 응답한다.

#### 실패
* limit이 숫자형이 아니면 400을 응답한다.
* offset의 숫자형이 아니면 4000을 응답한다.

### 예제

```javascript
const express = require('express');
const app = express();
const morgan = require('morgan');

const users  = [
  {id: 1, name: 'test1'},
  {id: 2, name: 'test2'},
  {id: 3, name: 'test3'},
];

app.use(morgan('dev'));

app.get('/users', (req, res)=>{
  res.json(users);
});

app.listen(3000, ()=>{
  console.log('Server ... port 3000');
});

module.exports = app;
```
* `/users` 해당 유저의 모든 정보를 가져온다

`index.spec.js`
```javascript
const app = require('./index');
const requrest = require('supertest');


describe('GET /users는', ()=>{
  it('모든 유저정보가 있는지 확인한다.'(done)=>{
    requrest(app)
    .get('/users')
    .end(err, res)=>{
      console.log(res.body);
      done();
    }
  });
});
```

* users API에대한 간단한 테스트 코등
* 비동기형식으로 동작하기 때문에 `it('...',(done))`의 `done`인자로 순차적인 진행을 진행해야한다.
* 모든 작업이 끝나면 `done()`을 호출시켜야 한다.
