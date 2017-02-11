Redis
===


### Redis 란?

### Redis 설치
```
$ wget http://download.redis.io/redis-stable.tar.gz
$ tar xvzf redis-stable.tar.gz
$ cd redis-stable
$ make
```

### 실행
```
$ redis-server
```


## Redis Node 연동

### 모듈설치
```
npm install redis --save
```

### 연동
```javascript
const
    redis = require('redis'),
    db_info = require('../secret/secret');

module.exports = (router) =>{

    global.client = redis.createClient(db_info.redis.port, db_info.redis.host);

    client.on('connect', ()=>{
        console.log("Redis is connected ");
    });

    client.on('ready', ()=>{
        console.log("Redis is ready");
    });

    client.on('error', (err)=>{
        console.log('Redis error encountered : ', err);
    });

    client.monitor((err, res)=>{
        if(err){
            console.err(err);
        }else{
            console.info(res);
        }
    });

    client.on('monitor', (time, args, raw_reply)=>{
        console.log(time + ': ' + args + ' | ' + raw_reply);
    });

    router.use((req, res, next)=>{
        req.cache = client;
        next();
    });
    client.unref();
};
```
port, host에 대한 설정은 `secret.js` 값을 참조 한다. 디비 정보들은 다른 설정파일에서 관리하는게 바람직하다.

전역변수 client에 레디스 컨넥션 정보를 넣고 `router.use`를 사용해서 레디스 정보를 req.cache에 넣는다.

### 사용방법

```javascript
RedisDAO.getDiet(req.cache, redis_key_diet, (err, cached) => {
    callback(err, JSON.parse(cached));
});
```
위 소스코드 처럼 req.cache를 이용해서 사용, DAO 객체는 아래에서 설명



### Redis DAO
```javascript
const RedisDAO = {};

RedisDAO.getDiet = (reqCache, key, callback) => {
    return reqCache.get(key, callback);
};

RedisDAO.setDiet = (reqCache, key, value, callback) => {
    return reqCache.set(key, value, callback);
};

module.exports = RedisDAO;
```
`reqCache(redis)`를 기반으로 key, callback을 넘겨 받아 작업을 수행하고 해당 값을 리턴 해줌
