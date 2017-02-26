## Node Cron

### Node Cron 란?
```
*    *    *    *    *  수행할 명령어
┬   ┬   ┬   ┬   ┬
│   │   │   │   │
│   │   │   │   │
│   │   │   │   └───────── 요일 (0 - 6) (0 =일요일)
│   │   │   └────────── 월 (1 - 12)
│   │   └─────────── 일 (1 - 31)
│   └──────────── 시 (0 - 23)
└───────────── 분 (0 - 59)
```
반복작업을 도와준다. crontab의 기능을 거의 동일하게 노드에서 사용할 수 있는 모듈이다. 정말 쉽다.



### 설치

```
npm install node-cron --save
```

### 사용법
```javascript
let task = cron.schedule('45 0 * * *', ()=> {
  console.log('진행할 작업');
});
task.start(); //cron start
```

### 적용 코드

```javascript
let task = cron.schedule('45 0 * * *', ()=> {

  const tasks = [
    (callback) => {
      RedisDAO.deleteByKeyPattern(client, RedisDAO.keys_pattern, (err) => {
        callback(err);
      });
    },

    (callback) => {

      request.get(URL.diet_normal, (err, res, html) => {
        if (!err && res.statusCode === 200) {
          let $ = cheerio.load(html);
          let diet = null;

          $('.tbline31 tr').each(() => {
            diet = '기존식단\r\n';
            diet += '----------아침---------\r\n';
            diet += $(this).find("td").eq(0).text() + '\r\n';
            diet += '----------점심---------\r\n';
            diet += $(this).find("td").eq(1).text() + '\r\n';
            diet += '----------저녁---------\r\n';
            diet += $(this).find("td").eq(2).text();
          });

          callback(err, diet);
        }
      });
    },

    (diet, callback) =>{
      RedisDAO.setByKey(client, RedisDAO.key_diet_normal, JSON.stringify(diet), (err) => {
        callback(err);
      });
    },
  ];

  async.waterfall(tasks, (err)=>{
    if(!err){
      console.info('Crontab Success');
    }else{
      console.error(err);
    }
  });
});

task.start(); //cron start
```

#### 작업순서
**매일 12시 45분에 실행**
1. `Redis Key Pattern` 삭제 (해당 키와 동일한 패턴은 복수개 삭제한다)
2. 식단 정보 파싱
3. 파시정보 `Redis`에 저장
