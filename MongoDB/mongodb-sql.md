
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

