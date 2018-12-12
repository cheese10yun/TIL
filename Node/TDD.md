## TDD in Node.js

## 목차

- [TDD in Node.js](#tdd-in-nodejs)
- [목차](#%EB%AA%A9%EC%B0%A8)
- [Mocha](#mocha)
- [특징](#%ED%8A%B9%EC%A7%95)
- [예제](#%EC%98%88%EC%A0%9C)
- [Should](#should)
- [예제](#%EC%98%88%EC%A0%9C-1)
- [SuperTest](#supertest)
  - [테스트 시나리오](#%ED%85%8C%EC%8A%A4%ED%8A%B8-%EC%8B%9C%EB%82%98%EB%A6%AC%EC%98%A4)
    - [성공](#%EC%84%B1%EA%B3%B5)
    - [실패](#%EC%8B%A4%ED%8C%A8)
  - [예제](#%EC%98%88%EC%A0%9C-2)
- [NPM 테스트 스크립트](#npm-%ED%85%8C%EC%8A%A4%ED%8A%B8-%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8)
- [describe 사용법](#describe-%EC%82%AC%EC%9A%A9%EB%B2%95)
  - [only](#only)

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

## NPM 테스트 스크립트

```
"scripts": {
  "test": "mocha index.spec.js"
},
```
*  긴명령어 없이 간단하게 실행가능


## describe 사용법

### only

```javascript
describe.only(....)
it.only(...)
before(...)
```
* `only` 메서드를 통해서 이미 많이 작성된 TDD 케이스중에 `only`옵션만 붙은 것만 실행 시킬 수 있다.
* `before(()` 작성된 TDD 케이스 실행 이전에 특정 함수를 실행 시킬 수 있다. 예를 들어 더미데이터 값을 입력하고 그 더미데이터에 대해서 TDD를 수행시킬 수 있다.
* `--watch` : `package.json`에서 모카 실행 옵션에 `--watch`을 작성하면 코드가 변경되면 자동으로 TDD을 돌려준다
