
```
db
{ "result": "users" }
```
* 현재 사용하고 있는 데이터베이스를 확인 

```
use video
db
{ "result": "video" }

```
* 사용할 데이터베이스 선택

```
db.movies
```
* 사용할 컬렉션 선택
* 이제 셸에서 컬렉션에 접근하여 데이터베이스 작업을 수행할 수 있음


# insertOne

```
movie = {
  "title":"Start Wars-2",
  "director": "AJ",
  "year": 1999
}

db.movies.insertOne(movie)
```

# explain

```
db.users.find(
  {
    "username": "user101"
  }
).explain("executionStats")
```
* executionStats 모드는 인덱스를 이용한 쿼리 정보를 확인할 수 있다.

# createIndex

```
db.users.createIndex( { "username": 1 } ) // 단일 인덱스
db.users.createIndex({ // 복합 인덱스
  "age":1,
  "username":1
})
```
* index 생성

# sort
```
db.users.find({"age": 21}).sort({"username": -1})
```


# 비교
```
db.users.find({"age": {"$gte": 21, "$lt": 30}})
```