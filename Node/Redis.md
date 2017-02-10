Redis
===


## Redis 란?

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
