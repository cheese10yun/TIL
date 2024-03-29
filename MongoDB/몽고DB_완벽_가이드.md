> [몽고 디비 완벽 가이드](http://www.yes24.com/Product/Goods/97980005)

# 1 몽고DB 소개

* 몽고DB는 도큐먼트 지향 데이터베이스다. 관계형 모델을 사용하지 않던 주된 이유는 분산 확장을 쉽게 하기 위함 이지만 다른 이점도 있다.
* 도큐먼트 지향 데이터베이스에서는 행 개념 대신에 보다 유연한 모델인 도큐먼트를 사용한다.
* 몽고DB에서는 도큐먼트의 키와 값을 미리 정의하지 않는다. 따라서 고정된 스키마가 없다.

## 확장 가능한 설계

몽고DB는 분산 확장을 염두에 두고 설계 됐다. 도큐먼트 지향 데이터 모델은 데이터를 여러 서버에 더 쉽게 분산하게 해준다. 도큐먼트를 자동으로 재분배하고 사용자 요청을 올바른 장비에 라우팅함으로써 클러스터 내 데이터 양과 부하를 조절할 수 있다. 몽고DB 클러스터의 토폴리지나 데이터베이스 연결의 다른 쪽 끝에 단일 노드가 아닌 클러스터가 있는지 애플리케이션에서 분명히 알 수 있다. 더 큰 부하를 지원하도록 기존에 배포된 애플리케이션의 토폴로지를 변경할 때도 마찬가지로 애플리케이션 로직은 그대로 유지할 수 있다.

## 다양한 기능

### 인덱싱

몽고DB는 일반적인 보조 인덱스를 지원하며, 고유, 복합, 공간 정보, 전문 인덱싱 기능도 제공한다. 중첩된 도큐먼트 및 배열과 같은 계층 구조의 보조 인덱스도 지원하며, 개발자는 모델링 기능을 자신의 애플리케이션에 가장 적합한 방식으로 최대한 활용할 수 있다.

### 집계

몽고DB는 데이터 처리 파이프라인 개념을 기반으로 한 집계 프레임워크를 제공한다. 집계 파이프라인은 데이터베이스 최적화를 최대한 활용해, 서버 측에서 비교적 간단한 일련의 단예로 데이터를 처리함으로써 복잡한 분석 엔진을 구축하게 해준다.

### 특수한 컬렉션 유형

몽고DB는 로그와 같은 최신 데이터를 유지하고자 세션이나 고정 크기 컬력센과 같이 특정 시간에 만료해야 하는 데이터에 대한 유효 시간 TTL 컬렉션을 지원한다. 또한 기준 필터와 일치하는 도큐먼트에 한정된 부분 인덱스를 지원함으로써 효율성을 높이고 필요한 젖아 공간을 줄인다.

### 파일 스토리지

몽고DB는 큰 파일에 파일 메타데이터를 편리하게 저장하는 프로토컬을 지원한다.

관계형 데이터베이스에 공통적으로 사용되는 일부 기능, 특히 복잡한 조인은 몽고DB에 존재하지 않는다. 몽고DB 3.2에 도입단 $lookup 집계 연산자를 사용함으로써 매우 제한된 방식으로 조인하도록 지원한다.

## 고성능

몽고DB에서는 동시성과 처리량을 극대화하기 위해 와이어드타이거 스토리지 엔진에 기회적 락을 사용했다. 따라서 캐시처럼 제한된 용량의 램으로 쿼리에 알맞은 인덱스를 자동으로 선택할 수 있다. 몽고DB는 강력한 성능을 제공하면서 관계형 시스템의 많은 기능을 포함한다. 일부 기능의 경우 데이터베이스 서버는 처리와 로직을 클라이언트 측에 오프로드한다.(드라이버 또는 사용자의 애플리케이션 코드에의해 처리된다.)

# 2 몽고DB 기본

* 몽고DB 데이터의 기본 단위는 도큐먼트이며, 이는 관계형 데이터베이스의 행과 유사하다.
* 같은 맥락에서 컬렉션은 동적 스키마가 있는 테이블과 같다.
* 몽고DB의 단일 인스턴스는 자체적인 컬렉션을 갖는 여러 개의 독립적인 데이터베이스를 호스팅한다.
* 모든 도큐먼트 컬렉션 내에서 거유한 특수키인 "_id"를 가진다.
* 몽고DB는 몽고 쉘 이라는 도구로 몽고DB 인스턴스를 관리하고 몽고DB 쿼리 언어로 데이터를 조작하기 위한 내장 지원을 제공한다.

## 도큐먼트

* 도큐먼트의 키는 문자열이다. 몇 가지를 제외하면 어떤 UTF-8 문자든 쓸 수 있다.
* 몽고DB는 데이터형과 대소문자를 구분한다.
    * { "count": 5 }, { "count": "5" } 서로 다르다.
* 몽고DB에서는 키가 중복될 수 없다.

## 컬렉션

컬렉션은 도큐먼트의 모음이다. 몽고DB의 도큐먼트가 관계형 데이터베이스의 행에 대응된다면 컬렉션은 테이블에 대응된다고 볼 수 있다.

### 동적 스키마

컬렉션은 동적 스키마를 가진다. 하나의 컬렉션 내 도큐먼트들이 모두 다른 구조를 가질 수 있다는 의미이다. 예를 들어 다음 도큐먼트들을 하나의 컬렉션에 저장할 수 있다.

```micronaut-mongodb-json
{
  "greeting": "Hello, world!",
  "views": 3
}
{
  "signoff": "Good night, and good luck"
}
```

**도큐먼트에 별도의 스키마가 필요 없는데 왜 하나 이상의 컬렉션이 필요한지는 몇가지 합당한 이유가 있다**

* 인덱스를 만들면 도큐먼트는 특정 구조를 가져야 한다. 고유 인덱스일 경우 특히 더 그렇다. 이러한 인덱스는 컬렉션별로 정의한다. 같은 유형의 도큐먼트를 하나의 컬렉션에 넣음으로써 컬렉션을 효율적으로 인덱싱할 수 있다.

### 서브컬렉션

서브컬렉션의 네임스페이스에 `.` 문자를 사용해 컬렉션을 체계화한다. blog.posts, blog.authors라는 컬렉션을 가질 수 있다. 이는 단지 체계화를 위함이며 blog 컬렉션이나 자식 컬렉션과는 아무런관계가 없다. 심지어 blog 컬렉션이 없어도된다. 서브컬렉션은 특별한 속성은 없지만 여러 몽고DB 툴에서 지원하므로 유용하다.

### 데이터베이스

몽고DB는 컬렉션에 도큐먼트를 그룹화할 뿐 아니라 데이터베이스에 컬렉션을 그룹지어 놓는다. 몽고DB의 단일 인스턴스는 여러 데이터베이스를 호스팅할 수 있으며, 각 데이터베이스를 완전히 독립적으로 취급할 수 있다.

## 몽고DB 셸 소개

몽고DB 명령행에서 몽고DB 인스턴스와 상호작용하는 자바스크립트 셸을 제공한다.

```
$ mongo
MongoDB server version: 4.4.18
Welcome to the MongoDB shell.
For interactive help, type "help".
For more comprehensive documentation, see
	https://docs.mongodb.com/
Questions? Try the MongoDB Developer Community Forums
	https://community.mongodb.com
	
	
> x = 200
200
> x / 5;
40
> Math.sin(Math.PI / 2)
1
> new Date("2023-01")
ISODate("2023-01-01T00:00:00Z")
```

### 몽고DB 클라이언트

셸은 독자적으로 사용이 가능하다. 셸은 시작할 때 서버의 test 데이터베이스에 연결하고, 데이터베이스 연결을 전역 변수에 db에 할당한다. 셸에서는 주로 이 변수를 통해 몽고DB에 접근한다.

### 셸 기본 작업

### 생성

```
db.movies
movie = {
  "title":"Start Wars",
  "director": "AJ",
  "year": 1977
}

db.movies.insertOne(movie)
db.movies.find().pretty()

[
  {
    "_id": {"$oid": "63d414430db2d501a49b3d9c"},
    "director": "AJ",
    "title": "Start Wars",
    "year": 1977
  }
]
```

insertOne 함수는 컬렉션에 도큐먼트를 추가한다. 우선 자바스크립트 객체인 movie라는 지역 변수를 생성한다. 그리고 insertOn 함수를 이용해 movies 컬렉션에 저장한다. `_id` 칼럼이 추가 되고 key/value 형태로 저장된다.

### 읽기

```spring-mongodb-json
db.movies.findOne()
[
  {
    "_id": {
      "$oid": "63d414430db2d501a49b3d9c"
    },
    "director": "AJ",
    "title": "Start Wars",
    "year": 1977
  }
]
```

find, findOne은 쿼리 도큐먼트 형태로 조건 전달도 가능하다. 따라서 쿼리에서 일치하는 도큐먼트로 결과를 제한한다.

### 갱신

updateOne으로 갱신을 진행하낟. 첫 번째 매개변수는 수정할 도큐먼트를 찾는 기준이고, 두 번째 매개변수는 해당 작업을 설명하는 도큐먼트이다. 영화에 ㄹ뷰 기능을 사용을 하기로 한다면 다음과 같이 진행된다.

```
db.movies.updateOne(
{title: "Start Wars"},
{$set: {comments: []}}
)

```

### 삭제

deleteOne, deleteMany는 도큐컨트를 데이터베이스에서 영구적으로 삭제한다.

```
db.movies.deleteOne({title:"Start Wars-2"})

[
  {
    "acknowledged": true,
    "deletedCount": 1
  }
]
```

deleteMany는 필터에 만족하는 모든 도큐먼트를 삭제하는 경우 사용한다.

## 데이터형

JSON 형식으로 다음과 같은 데이텨 형을 지원한다.

* null
* boolean
* number
* string
* object
* array

몽고DB는 JSON 성질을 유지하면서 추가적인 데이터 형을 지원한다.

* null: 값이 존재하지 않은 필드를 표현 하는데 사용한다.
* boolean: 참거짓
* number: 셸은 64비트 부동 소수를 기본으로 사용한다.
* string: 어떤 UTF-8 문자열이든 문자열형으로 표현 가능
* date: 1970년 1월 1일 부터 시간을 1/1000초 단위로 나타내는 64비트 정수로 날짜 표현, 표준 시간대는 저장하지 않는다.
* regular expression: 쿼리는 자바스크립트 정규 표현식 문법을 사용할 수 있다.
* array: set, list 형태로 표현할 수 있다.
* 내장 도큐먼트: `{"x" : {"foo": "bar"}}` 도큐먼트는 부모 도큐먼트의 값으로 내장된 도큐먼트 전체를 포함할 수 있다.
* 객체 ID: 도큐먼트용 12 바이트 ID
* 이진 데이터: 이진 데이터는 임의의 바이트 문자열이며 셸에서는 조작이 불가능하다. 이진 데이터는 데이터베이스에 UTF-8이 아닌 문자열을 저장하는 유일한 방법이다.
* 코드: `{"x": function() {....}}` 쿼리와 도큐먼트 임이의 자바스크립트 코드를 포함할 수 있다.

### 배열

배열은 정렬 연산(리스트, 스택, 큐)과 비정렬 연산(셋) 호환성있게 사용한 값이다.

```json
{
  "things": [
    "pie",
    3.14
  ]
}
```

서로 다른 데이터형 값으로 포함할 수 있다. 중첩 배열도 가능하다. 배열에 쿼리하거나 배열의 내용을 이용해 인덱스를 만들 수 있다. 자주 쓰는 쿼리라면 "things" 키에 인덱스를 생성해 쿼리 속도를 향상시킬 수도 있다.

### 내장 도큐먼트

도큐먼트는 키 값에 대한 갑싱 될 수 있는데 이를 내장 도큐먼트라고 한다.

```json
{
  "name": "AJ",
  "address": {
    "street": "123 Park",
    "city": "A-Town",
    "state": "NY"
  }
}
```

address에 대한 key/value를 갖는 내장 도큐먼트이다.

### _id, ObjectId

몽고DB에 저장되는 모든 도큐컨트는 _id 키를 가진다. 하나의 컬렉션에서 모든 도큐먼트는 고유한 id를 가지며 이 값은 컬렉션 내 모든 도큐먼트가 고유하게 식별된다.

ObjectId는 여러 장비에 걸쳐 전역적으로 고유하게 생성하기 쉽게 설계됐다. 자동 증가하는 기본 키처럼 전통적인 것이 아닌 ObjectId를 사용하는 주요 이유는 몽고DB는 분산 특성이기 때문이다. ObjectId는 12바이트 스토리지를 사용하며 24자리 16진수 만자열 표현이 가능하다. 바이트당 2자리를 사용한다. ObjectId가 흔히 16진수 문자열로 표현되긴 하지만 실제로 문자열은 저장된 데이터의 두 배만큼 길다는 점을 알아두자.

# 3 도큐먼트 생성, 갱신, 삭제

## 도큐먼트 삽입

insertOne 메서드를 사용한다.

```
db.movies.insertOne({title: "타짜"})
```

그러면 도큐먼트에 _id 키가 추가되고 몽고DB에 저장된다.

### insertMany

여러 도큐먼트를 컬렉센여 삽입하려면 insertMany로 도큐먼트 배열을 데이터베이스에 전달한다. 코드가 삽입된 각 도큐먼트에 대해 데이터베이로 왕복하지 않고 도큐먼트를 대량으로 bulk insert 가능하다.

```
db.movies.insertMany(
  [
    {"title": "A1"},
    {"title": "A2"},
    {"title": "A3"},
    {"title": "A4"}
  ]
);
```

수천 개의 도큐먼트를 한 번에 전송하면 도큐먼트 삽입이 매우 빨라진다. 몽고DB는 현재 버전에서는 48메가바이트보다 큰 메시지를 허용하지 않으므로 한 번에 일괄 삽입할 수 있는 데이터의 크기에는 제한이 있다.

insertMany를 사용해 대량 삽입할 때 배열 중간에 있는 도큐먼트에서 특정 유형의 오류가 발생 하는 경우, 정렬 연산을 선택했는지 혹은 비정렬 연산을 선택해는지에 따라 발생하는 상황이 달라진다. insertMany에 옵션으로 도큐먼트가 제공된 순서대로 삽입되도록 옵션 도큐먼트에 "ordered" 값에 true를 지정한다(기본 값). false로 지정하면 몽고DB가 성능을 개선하려고 삽입을 재배열할 수 있다. ordered true인 경우 오류 발생 이후 부터는 insert 하지 않는 반면 ordered flase 경우 오류가 발생해도 모든 도큐먼트에 대해서 insert를 진행한다.

## 도큐먼트 삭제

deleteOne은 필터와 일치하는 첫 번째 도큐먼트를 삭제 한다. deleteMany는 필터와 일치하는 모든 도큐먼트를 삭제 한다. 컬렉션 전체를 삭제하려면 drop을 사용하는 것이 더 빠르다.

## 도큐먼트 갱신

updateOne, updateMany는 필터 도큐먼트를 첫 번째 매개변수로, 변경 사항을 설명하는 수정자 도큐먼트를 매개변수로 사용한다. replaceOne도 첫 번째 매배변수로 필터를 사용하지만 두 번째 매개변수는 필터와 일치하는 도큐먼트를 교체할 도큐먼트이다.

갱신은 원자적으로 이루어진다, 갱신 요청 두 개가 동시에 발생하면 서버에 먼저 도착한 요청이 적용된 후 다음 요청이 적용된다. 따라서 여려 개의 갱신 요청이 빠르게 발생하더라도 결국 마지막 요청이 최후의 승리자가 되므로 도큐먼트는 변질 없이 안전하게 처리된다. 기본 동작을 원치 않으면 도큐먼트 버저닝 패턴을 고려하자.

### 도큐먼트 치환

replaceOne은 도큐먼트를 새로운 것으로 완전히 치환한다.

```
var j = db.users.findOne({"name": "J"});
j.relationships = {
"friedns": j.friedns,
"enmies": j.enmies
};

j.username = j.name;

delete j.friedns;
delete j.enmies;
delete j.name;

db.users.replaceOne({"name": "J"}, j)

db.users.find()
```

### 갱신 연산자

도큐먼트의 특정 부분만 갱신하는 경우에는 워나적 갱신 연산자를 사용한다. 갱신 연산자는 키를 변경, 추가, 제거하고, 배열과 내장 도큐먼트를 조작하는복잡한 갱신 연산을 지정하는 특수키다.

```
use analytics;

var a = {"pagevies": 12}

db.analytics.insertOne(a)

db.analytics.find()

db.analytics.updateOne(
  {"_id": ObjectId("63d42d790db2d501a49b3db2")},
  {"$inc": {"pagevies": 1}}
)

db.analytics.find()
```

$inc 제한자를 통해서 값을 증가 시킬 수 있다.

$set 제한자는 필드가 존재하지 않는다면 새 필드가 생성된다. 이 기능은 스키마를 갱신하거나 사요아 정의를 추가할 때 편리하다.

# 쿼리

## find 소개

find의 첫 매개변수에 따라 어떤 도큐먼트를 가져올지 결정된다. 빈 쿼리 경우 컬렉션 내 모든 것과 일치한다. 즉 전체 도큐먼트를 반환한다.

```
db.users.find({"name": "J"})
```

name이 J인 것을 조회, and 조건으로 더 추가 할 수 있다.

### 반환받을 키 지정

```
db.users.find({}, {"name": 1})
```

도큐먼트의 필드들을 선태적으로 조회할 수 있다. 네트워크, 클라이언트에서 디코딩하는 데 드는 시간과 메모리를 줄여 줄 수 있다. `_id`는 항상 반환된다.

## 쿼리 조건

### 쿼리 조건절

`<`, `<=`, `>`, `>=`에 해당하는 `$it`, `$lte`, `$gt`, `$gte`다

```
db.users.find({"age": {"$te": 18, "$lte": 30}})
```

18이상, 30 이하의 도큐먼트를 모두 찾는다.

### OR 쿼리

`$in`는 하나의 키를 다야한 값과 비교하는 쿼리로,`$or`는 더 일반적이며, 여러 키를 주어진 값과 비교하는 쿼리에 사용한다.

```
db.users.find(
  { 
    name: { "$in": ["J", "w"] }
  }
)
```

`$in`은 매우 유연해 여러 개의 값을 쓸 수 있을 뿐만 아니라 서로 다른 데이터형도 쓸 수 있다. `$nin`은 `$in` 반대로 배열 내 조건과 일치하지 않은 도큐먼트를 반환한다.

### `$not`

`$not`은 메타 조건 절이며 어떤 조건에도 적용할 수 있다.

## 형 특정 쿼리

### null

null은 일치하는 것을 찾는다. 여기서 중요한 것은 값이 null 것과, 값 자체가 존재하지 않는 것도 null로 간주하기 때문에 쿼리하면 결과가 나온다. 그래서 값 자체가 null인 키만 찾고 있다면 `$exists`조건을 사용해서 null 존쟈 여뷰를 확인하면 된다.

```
db.users.find({"username": {"$eq": null, "$exists": true}});
```

### 내장 도큐먼트에 쿼리하기

```
db.users.find({"relationships": {"friedns": 32, "enmies": 2}})

db.users.find(
    {"relationships.enmies": 32, "relationships.enmies": 2}
)
```

서브도큐먼트 쿼리에 정확하게 일치 해야한다. 그러지 않으면 조회되지 않는다. 내장 도큐먼트키를 점 표기법으로 사용할 수 있다.

# 5 인덱싱

## 인덱스 스캔

인덱스를 사용하지 않는 쿼리를 컬렉션 스캔 이라하며, 전체 내용을 살펴봐야하는 Full Scan을 의미한다. rows 282,101 유저가 있는 경우 실행 계획을 보면

```
db.users.find(
  {
    "username": "user101"
  }
).explain("executionStats")
```

```json
[
  {
    "executionStats": {
      "executionSuccess": true,
      "nReturned": 1,
      "executionTimeMillis": 56,
      "totalKeysExamined": 0,
      "totalDocsExamined": 282101,
      "executionStages": {
        "stage": "COLLSCAN",
        "filter": {
          "username": {
            "$eq": "user101"
          }
        },
        "nReturned": 1,
        "executionTimeMillisEstimate": 1,
        "works": 282103,
        "advanced": 1,
        "needTime": 282101,
        "needYield": 0,
        "saveState": 282,
        "restoreState": 282,
        "isEOF": 1,
        "direction": "forward",
        "docsExamined": 282101
      }
    },
    "ok": 1,
    "queryPlanner": {
      "plannerVersion": 1,
      "namespace": "users.users",
      "indexFilterSet": false,
      "parsedQuery": {
        "username": {
          "$eq": "user101"
        }
      },
      "winningPlan": {
        "stage": "COLLSCAN",
        "filter": {
          "username": {
            "$eq": "user101"
          }
        },
        "direction": "forward"
      },
      "rejectedPlans": []
    },
    "serverInfo": {
      "host": "36b49af79a43",
      "port": 27017,
      "version": "4.4.18",
      "gitVersion": "8ed32b5c2c68ebe7f8ae2ebe8d23f36037a17dea"
    }
  }
]
```

totalDocsExamined는 몽고DB가 쿼리르 실행하면서 살펴본 도큐먼트 개수로 모든 도큐먼트에 대해서 조회를 진행 한것이다. nReturned 필드는 반환받은 결과의 개수를 보여준다. 1명만 존재하기 때문에 1을 응답받았다. 몽고DB가 쿼리에 효율적으로 응답하게 하려면 애플리케이션의 모든 쿼리 패턴에 인덱스를 사용해야 한다.

### 인덱스 생성

```
db.users.createIndex( { "username": 1 } )
```

username 필드에 인덱스를 만들려면 createIndex 컬렉션 메서드를 사용한다.

```josn
[
  {
    "executionStats": {
      "executionSuccess": true,
      "nReturned": 1,
      "executionTimeMillis": 7,
      "totalKeysExamined": 1,
      "totalDocsExamined": 1,
      "executionStages": {
        "stage": "FETCH",
        "nReturned": 1,
        "executionTimeMillisEstimate": 0,
        "works": 2,
        "advanced": 1,
        "needTime": 0,
        "needYield": 0,
        "saveState": 0,
        "restoreState": 0,
        "isEOF": 1,
        "docsExamined": 1,
        "alreadyHasObj": 0,
        "inputStage": {
          "stage": "IXSCAN",
          "nReturned": 1,
          "executionTimeMillisEstimate": 0,
          "works": 2,
          "advanced": 1,
          "needTime": 0,
          "needYield": 0,
          "saveState": 0,
          "restoreState": 0,
          "isEOF": 1,
          "keyPattern": {
            "username": 1
          },
          "indexName": "username_1",
          "isMultiKey": false,
          "multiKeyPaths": {
            "username": []
          },
          "isUnique": false,
          "isSparse": false,
          "isPartial": false,
          "indexVersion": 2,
          "direction": "forward",
          "indexBounds": {
            "username": ["[\"user102\", \"user102\"]"]
          },
          "keysExamined": 1,
          "seeks": 1,
          "dupsTested": 0,
          "dupsDropped": 0
        }
      }
    },
    "ok": 1,
    "queryPlanner": {
      "plannerVersion": 1,
      "namespace": "users.users",
      "indexFilterSet": false,
      "parsedQuery": {
        "username": {
          "$eq": "user102"
        }
      },
      "winningPlan": {
        "stage": "FETCH",
        "inputStage": {
          "stage": "IXSCAN",
          "keyPattern": {
            "username": 1
          },
          "indexName": "username_1",
          "isMultiKey": false,
          "multiKeyPaths": {
            "username": []
          },
          "isUnique": false,
          "isSparse": false,
          "isPartial": false,
          "indexVersion": 2,
          "direction": "forward",
          "indexBounds": {
            "username": ["[\"user102\", \"user102\"]"]
          }
        }
      },
      "rejectedPlans": []
    },
    "serverInfo": {
      "host": "36b49af79a43",
      "port": 27017,
      "version": "4.4.18",
      "gitVersion": "8ed32b5c2c68ebe7f8ae2ebe8d23f36037a17dea"
    }
  }
]
```

totalDocsExaminedrk 1으로 인덱스를 타서 조회를 진행한 것을 볼 수 있다. executionTimeMillis도 56 -> 7로 계선되었다. 인덱싱된 필드를 변경하는 쓰기 작업은 더 오래 걸린다. 데이터가 변경될 때마다 도큐먼트뿐 아니라 모든 인덱스를 갱신해야 하기 때문이다. 몽고DB에서 인덱스는 정형적인 관계형 데이터베이스의 인덱스와 거의 동일하게 작동한다.

### 복합 인덱스

쿼리 패턴은 두 개 이상의 키를 기반으로 인덱스를 작성하는 것은 흔한 케이스이다. 인덱스는 모든 값을 정렬된 순서로 보관하므로 인덱스 키로 도큐먼트를 정렬하는 작업이 훨씬 빨라지게 한다. 하지만 인덱스가 앞부분에 놓일 때만 정렬에 도움이 된다. 예를 들어 인덱스는 다음 정렬에 큰 도움이 되지 않는다.

```
db.users.find().sort({
  "age": 1,
  "username":1
})
```

age로 정렬한 후에 username으로 정렬하는데, username에 의한 완전정렬은 별로 도움이되지 않는다. 정렬을 최적화하려면 age, username에 인덱스를 만들어야 한다.

### db.users.find({"age": 21}).sort({"username": -1})

인덱스의 두 번째 필드로 인해 결과는 이미 적절한 순서로 정렬된다. 즉 몽고DB는 {"age": 21}과 일치하는 마지막 항목부터 순서대로 인덱스를 탐색한다. 이러한 쿼리는 매우 효율적이다. 몽고DB는 곧바로 정확한 나이로 건너뛸 수 있으며 인덱스 탐색은 데이터를 올바른 순서로 반환하므루ㅗ 결과를 정렬할 필요가 없다.

### db.users.find({"age": {"$gte": 21, "$lt": 30}})

범위 쿼리이며 여러 값이 일치하는 도큐먼트를 찾는다. 몽고DB는 인덱스에 있는 첫 번째 키인 "age"를 다음 처럼 사용해 일치하는 도큐먼트틀 반환받는다. 몽고DB가 인덱스를 사용해 쿼리하면 일반적으로 인덱스 순서에 따라 도큐먼트 결과를 반환한다.

### db.users.find({"age": {"$gte": 21, "$lt": 30}}).sort({"username": 1})

다중 값 쿼리 이지만 정렬을 포함하는 경우 이전 처럼 몽고DB는 검색 조건에 맞는 인덱스를 사용한다. 하지만 인덱스는 사용자명을 정렬된 순서로 반환하지 않으며, 쿼리는 사용자명에 따라 정렬된 결과를 요청한다. 몽고DB가 이미 원하는 순서대로 도큐먼트가 정렬된 인덱스를 단순히 통과하지 않고, 결과를 반환하기 전에 메모리에서 정렬 해야 함을 의미한다.

검색 조건에 일치하는 결과에 따라 속도가 달라지며 결과가 너무 많은 경우 동작이 느려지거나 전혀 작동하지 않ㄹ을 수도 있다. 결고결과가 32메가 이상이면 몽고DB는 데이터가 너무 많아 정렬을 거부한다는 오류를 내보낸다. 해당 오류를 피하고 싶다면 정렬 작업을 지원하는 인덱스를 생성하고 sort, limit 방식을 함께 사용해 결과를 줄여야한다.

## 몽고DB가 인덱스를 선택하는 방법

쿼리가 들어오면 몽고DB는 쿼리 모양을 확인한다. 모양은 검색할 필드와 정렬 여부 등 추가 정보와 관련 있다. 시스템은 이 정보를 기반으로 쿼리를 충족하는 데 사용할 인덱스 후보 집합을 식별한다.

쿼리가 들어오고 인덱스가 5개 중 3개가 쿼리 후보로 식별됐다고 가정해보자. 몽고DB는 각 인덱스 후보에 하나씩 총3 개의 쿼리 플랜을 만들고, 각각 다른 인덱스를 사용하는 3개의 벙렬된 스레드에서 쿼리를 실행한다. 어떤 스레드가 가장 빨리 결과를 반환하는지 확인 하기 위함이다.

이 과정을 레이스라고 하며 가장 먼저 목표 상테에 도달하는 쿼리 플랜이 승자가 된다. 또한 앞으로 동일한 모양을 가진 쿼리에 사용할 인덱스로 선택된다는 점이 중요하다.

쿼르 스레드가 레이스에서 이기려면, 모든 쿼리 결과를 가장 먼저 반환하거나 결과에 대한 시범 횟수를 정렬 순서로 가장 먼저 반환해야 한다. 인메모리 정렬을 하면 비용이 많이 들기 때문에 정렬 순서는 중요한 부분이다.

여러 쿼리 플랜이 서로 경쟁함으로써, 모양이 동일한 후속 쿼리가 있을 때 몽고DB 서버에서 어떤 인덱스를 선택할지 알 수 있다. 서버는 쿼리 플랜의 캐시를 유지하는데, 승리한 플랜은 차후 모양이 같은 쿼리에 사용하기 위해 캐시에 저장된다. 시간이 지나거나 컬렉션과 인덱스가 변경되면 쿼리 플랜이 캐시에 제거되고, 몽고DB는 다시 가능한 쿼리 프랠ㄴ을 실험해 해당 컬렉션 및 인덱스 집합에 가장 적합한 플랜을 찾는다.

### 복합 인덱스 사용

먼저 인덱스의 선택성을 고려해야한다. 쿼리 패턴에서 스캔할 레코드 개수를 인덱스가 얼마나 최소화하는지 중요하다. 쿼리를 충족하는 데 필요한 모든 작업을 고려해야 하며 때로는 트레이드오프가 필요하다. 예를 들어 정렬 처리되는 방식을 고려 해야한다. 약 백만 개의 레코드가 포함된 학생 데이터셋을 사용한다.

```
{
  "_id": ObjectId(xxxxx)
  "student_id": 0,
  "scores": [
    {
      "type": "exam",
      "score": 38.123
    },
    {
      "type": "quiz",
      "score": 78.221
    },
    {
      "type": "homework",
      "score": 74.234
    },
    {
      "type": "homework",
      "score": 74.523
    }
  ]
}
```

인덱스 두 개로 시작해서 몽고DB가 쿼리를 총족하기 위해 이러한 인덱스를 어떻게 사용하느지 살펴 보자.

```
db.students.createIndex({"student_id": 1)
db.students.createIndex({"student_id": 1, "class_id": 1})
```

인덱스 설계 시 고려할 몇 가지 문제점을 다음 데이터셋에서 살펴본다. 다음 쿼리르 보자

```
db.students
  .find({"student_id": {"$gt": 50000}, "class_id": 54})
  .sort({"student_id": 1})
  .explain("executionStats")
```

이 쿼리에서 ID 50000 보다큰 모든 레코드를 요청한다. 전체 레코드의 절반 정도, 또한 검색을 ID가 54인 클래스에 대한 레코드로 제한한다. 데이터셋에는 약 500개의 클래스가 있다. 마지막으로 student_id를 기준으로 오름차순으로 정렬한다. explain을 통해서 쿼리 실행 계획을 살펴보자.

executionStats에서 totalKeysExamined는 몽고DB가 결과셋을 생성하기 위해 인덱스 내에서 몇 개의 키를 통과 했는지 나타낸다. totalKeysExamined nReturned와 비교하면 몽고DB가 쿼리와 일치하는 도큐먼트를 찾을려고 얼마나 많은 인덱스를 통과했는지 알 수 있다. 예제에서는 일치하는 도큐먼트 9903개를 찾으려고 인덱스키 85만 477개를 검사했다.

쿼리를 충족하는 데 사용된 인덱스가 선택적이지 않았음을 의미한다. executionTimeMillis를 보면 4.3초가 넘게 걸렸다. 선택성은 인덱스 설계 시 핵심 목표이므로 예제 쿼리에서 잘못된 부분을 파악해보자.

```
db.students
  .find({"student_id": {"$gt": 50000}, "class_id": 54})
  .sort({"class_id": 1})
  .hint({"class_id": 1})
  .explain("executionStats")
```

class_id가 54인 도큐먼트는 500개 정도 이므로 선택적인 기준으로 class_id 기반으로 인덱스를 사용하는 것이 좋다. 몽고DB가 데이터베이스가 특정 인덱스를 사용하도록 강제 하는 두 가지 방법을 제공한다. 쿼리 플래너의 결과를 재정의하는 방법은 매우 신중히 사용 되어야 하기 때뭄ㄴ에 운영 환경에서는 사용하기는 어렵다. 다음 방식은 위 처럼 hint 메서드를 사용하여 인덱스를 지정할 수 있다.

그러나 더 핵심 적인 부분은 쿼리 패턴에 더 나은 인덱스 순서를 지정하는 것이다.

```
db.students.createIndex({"class_id": 1, "student_id": 1})
```

모든 데이터셋에 해당되지는 않지만, 일반적으로 동등 필터를 사용할 필드가 다중값 필터를 사용할 필드보다 앞에 오도록 복합 인덱스를 설계해야한다.

다음 예제 쿼리는 필터링 대상 필드 중 하나를 기준으로 정렬하므로 특수한 복합 인덱스 문제를 나타낸다.

```
db.students
  .find({"student_id": {"$gt": 50000}, "class_id": 54})
  .sort({"fianl_grade": 1})
  .explain("executionStats")
```

explain을 확인해보면 서정된 쿼리 플랜에 "SORT" 단계가 포함되며, 이제 인메모리 정렬을 수행함을 알 수 있다. 인덱스를 더 잘 설계하면 인메모리 정렬을 피할 수 있다. 이를 통해 데이터셋 크기와 시스템 부화의 관련해 보다 쉽게 확장할 수 있다. 하지만 복합 인덱스를 설게할 때는 일반적으로 트레이드오프가 있다.

복합 인덱스에서 자주 발생하는 문제로, 인메모리 정렬을 피하려면 반환하는 도큐먼트 개수보다 더 많은 키를 검사해야한다. 인덱스를 사용해 정렬하려면 몽고DB가 인덱스 키를 순서대로 살펴볼 수 있어야 한다. 즉 복합 인덱스 키 사이에 정렬 필드를 포함해야 한다.

새로운 복합 인덱스의 키는 [class_id, final_grade, student_id]와 같이 정렬돼야 한다. 정렬 구성 요소는 동등 필더 바로 뒤, 다중값 필터 앞에 포함한다. 이 인덱스는 쿼리에서 고려하는 키 집합을 매우 선택적으로 좁힌다. 그런 다움 몽고DB는 인덱스의 동등 필터와 일치하 키 3개를 통해 필터와 일치하는 레코드를 식별한다. 이 복합 인덱스는 몽고DB가 결과 셋에 포함될 도큐먼트보다 더 많은 도큐먼트의 키를 검사하게 한다. 그러나 인덱스를 사용해 도큐먼트를 정렬함으로써 실행 시간을 절역할 수 있다.

```
db.students.createIndex({"student_id": 1, "final_grade:": 1 ,"class_id": 1})

db.students
  .find({"student_id": {"$gt": 50000}, "class_id": 54})
  .sort({"fianl_grade": 1})
  .explain("executionStats")
```

expain을 확인 해보면 인메모리 정렬 대신 방금 만든 인덱스를 사용하는 것을 확인할 수 있다.

복합 인덱스를 설계할 때 다음과 같은 사항을 고려 하자

* 동등 필터에 대한 키를 맨 앞에 표시해야 한다.
* 정렬에 사용되는 키는 다중값 필드 앞에 표시해야한다.
* 다중값 필터에 대한 키는 마지막에 표시해야 한다.

#### 키 방향 선택하기

지금까지 인덱스 항목은 모두 오름차순으로 정렬됐다. 하지만 두 개 혹은 그 이상의 검색 조건으로 정렬할 때는 인덱스 키의 방행이 서로 달라야한다. 복합 정렬을 서로 다른 방향으로 최적화라려면 방향이 맞는 인덱스를 사용해야 한다.

#### 커버드 쿼리 사용하기

인덱스가 쿼리가 요구하는 값을 모두 포함하면, 쿼리가 커버드 된다고 한다. 쿼리가 확실히 인덱스만 사용하게 하려면 "_id" 필드를 반환받지 않도록 반환 키를 지정 해야한다.

#### 암시적 인덱스

{"age": 1, "username":1}로 인덱스를 가지면 age 필드는 {"age": 1}로만 인덱스를 가질 때와 동일한 방법으로 정렬된다. 따라서 복합 인덱스는 자체적으로 {"age": 1} 인덱스를 가질 때 처럼 사용 된다.

B-Tree 구조와 비슷함

### $ 연산자의 인덱스 사용 방법

#### 비효율적인 연산자

일반적으로 부정 조건은 비효율적이다. $ne 쿼리는 인덱스를 사용하긴 하지만 잘 활용하지 못한다.

B-Tree 구조와 비슷함

#### 범위

복합 인덱스는 몽고DB가 다중 절 쿠리를 더 효율적으로 실행 하도록 돕는다.

#### OR 쿼리

현재 몽고DB는 쿼리당 하나의 인덱스만 사용할 수 있다. 다시 말해 {"x": 1}로 인덱스를 하나 생성하고 {"y": 1}로 또 다른 인덱스를 생성한 후 {"x": 123, "y": 456} 으로 쿼리를 실행하면 몽고DB는 생성한 인덱스 두 개 중 하나만 사용 한다. 유일한 예외는 "$or"이다. "$or"는 두 개의 쿼리를 수행하고 결과를 합치므로 "$or" 절마다 하나씩 인덱스를 사용할 수 있다.

### 객체 및 배열 인덱싱

몽고DB는 도큐먼트 내부에 도달해서 내장 필드와 배열에 인덱스를 생성하도록 허용한다. 내장 객체와 배열 필드는 복합 인데긋에서 최상위 필드와 결합될 수 있으며, 다소 특수한 경우를 제외하면 대부분 일반적인 인덱스 필드와 같은 방식으로 동작한다.

#### 내장 도큐먼트 인덱싱 하기

```json
{
  "username": "sid",
  "loc": {
    "ip": "1.2.3.4",
    "city": "YN",
    "state": "NY"
  }
}
```

```
db.users.createIndex({"loc.city": 1})
```

이렇게 내장 도큐먼트를 인덱싱 할 수 있다.
내장 도큐먼트 자체 ("loc")를 인덱싱하면 내장 도큐먼트의 필드 ("loc.city")를 인덱싱 할 때는 매우 다르게 동작한다. 서브 도큐먼트를 전체 인덱싱하면, 서브 도큐먼트 전체에 쿼리를할 때만 도움이 된다.

#### 배열 인덱싱하기

```
db.blog.createIndex({"comments.date": 1})
```

블로그 comments 배열에 date를 인덱싱을 위 처럼 할 수 있다. 한 게시물에 20개의 댓글이 달렸다면 도큐먼트 20개의 인덱스 항목을 가진다. 따라서 입력 갱신, 제거 작업을 하려면 모든 배열 요소가 갱신돼야 하므로 배열 인덱스를 단일값 인덱스보다 더 부담스럽게 만든다. "loc" 예제와 달리 배열 전체를 단일 개체처럼 인덱싱할 수 없다. 배열 필드 인덱싱은 배열 자체가 아니라 배열의 각 요소를 인덱싱하기 때문이다. **배열 요소에 대한 인덱스에는 위치 개념이 없기 때문에 "comments.4"와 같은 특정 배열 요소를 찾는 쿼리에 인덱스를 사용할 수 없다.**

배열의 특정 항목에 인덱스를 생성할 수는 있다.

```
db.blog.createIndex({"comments.10.votes": 1})
```

하지만 이 인덱스는 정확히 11번째 요소를 쿼리할 때만 유용하다. (배열의 인덱스는 0 부터 시작한다.)

인덱스 항목은 한 필드만 배열로부터 가져올 수 있다. 이는 여러 다중키 인덱스에 의해 인덱스 항목이 폭발적으로 늘어나는 것을 피하기 위함이다.

#### 다중키 인덱스가 미치는 영향

explain() 출력을 보면 인덱스가 다중키 인덱스인지 확인할 수 있으며, 다중키 인덱스가 사용됐다면 "isMultiKey" 필드가 true이다. 다중키 인덱스는 비다중키 인덱스 보다 약간 느릴 수 있다. 하나의 도큐먼트를 여러 개의 인덱스 항목이 가리킬 수 있으므로 몽고DB는 결과를 번환하기 전에 중복을 제거해야 한다.

### 인덱스 카디널리티

카디널리티는 컬렉션의 한 필드에 대해 고윳값이 얼마나 많은지 나타낸다. username, email 등이 카디널리티가 높은 필드이다. age, zip, code 같은 필드들은 카디널리티가 중간쯤에 해당한다.

예를들어 Susan인 여성을 찾는 경우 gender로 찾는 다면 검색 범위를 50%만 좁힐 수 있다. 반면 name에 인덱스가 있다면 결과 셋을 Susan 이라는 이름을 갖는 사용자들로 좁힌 후, 해당 도큐먼트를 참조해 성별을 확인할 수 있다.

## explain 출력

쿼리가 "COLLSCAN"를 사용하면 인덱스를 사용하지 않음을 알 수 있다.

* isMultiKey: false, 다중키 인덱스 사용 여부
* nReturned: 8449, 쿼리에 의해 반환된 도큐먼트 개수
* totalDocsExmined: 8449, 몽고DB가 디스크 내 실제 도큐먼트를 가리키는 인덱스 포인터를 따라간 횟수. 쿼리가 인덱스의 일부가 아닌 검색 조건을 포함하거나, 인덱스에 포함되지 않은 필드를 반환 하도록 요청한다면 몽고DB는 각 인덱스 항목이 가리키는 도큐먼트를 살펴봐야한다.
* totalKeysxmine: 8449, 인덱스가 사옹됐다면 살펴본 인덱스 항목 개수, 테이블 스캔을 했다면 조사한 도큐먼트 개수
* stage: IXSCAN: 몽고DB가 인덱스를 사용해 쿼리할 수 있었는지 여부, COLLSCAN은 인덱스로 쿼리할 수 없어 컬렉션 스캔을 수행했음을 뜻한다.
* needYields: 0, 쓰기 요청을 처리하도록 쿼리가 양보(일시 중지)한 횟수, 대기 중인 쓰기가 있다면 쿼리가 일시적으로 락을 해재하고 쓰기가 처리되게 한다. 대기 중인 쓰기가 없다면 0 이다.
* executionTimeMillis: 15, 데이터베이스가 쿼리하는 데 걸리는 시간, 밀리초 단위
* indexBounds: {...}, 인덱스가 어떻게 사용됐는지 설명하며 탐색한 인덱스 범위를 제공한다.

## 인덱스를 생성하지 않는 경우

어떤 쿼리는 인덱스가 없는 게 더 빠르다. 인덱스는 컬렉션에서 가져와야 하는 부분이 많을 수록 비효율적인데, 인덱스를 하나 사용하려면 두 번의 조회를 해야하기 때문이다. 한 번은 인덱스 항목을 살펴보고, 또 한 번은 도큐먼트를 가리키는 인덱스의 포인터를 따라 간다. 반면에 컬렉션 스캔을 할 때는 도큐먼트만 살펴보면 된다. 인덱스가 효율적인 공식은 없다. 실제 데이터 크기, 인덱스 크기, 도큐먼트 크기, 결과 셋의 평균 등에 따라 다르기 때문이다.

## 인덱스 종류

### 고유 인덱스

고유 인덱스는 각 값이 인덱스에 최대 한 번 나타나도록 보장한다. "_id"가 대표적이다.

### 복합 고유 인덱스

개별 키 값은 중복될 수 있지만 인덱스 항목의 모든 키에 걸쳐 값의 조합은 인덱스 최대에 한 번만 나타난다.

### 중복제거

기존 컬렉션에 고유 인덱스를 구축할 때 중복된 값이 있다면 실패한다.

#### 부분 인덱스

# 6 특수 인덱스와 컬렉션 유형

## 전문 검색을 위한 인덱스

정규 표현식으로 큰 텍스트 블록을 검색하면 쿼리 속도가 느리며, 문법과 같은 언어 특성을 반영하기도 쉽지 않다. (entry, entries와 일치 해야한다.) text 인덱스는 텍스트를 빠르게 검색하는 기능을 제공하며, 언어에 적합한 토큰화, 정지 단어, 형태속 분석 등 일반적인 검색 엔진 요구사항을 지원한다.

## TTL 인덱스

TTL 인덱스를 이용해서 각 도큐먼트에 유효 시간을 설정할 수 있다. 도큐먼트는 미리 설정한 시간에 도달하면 지워진다.

createdIndex의 두번째 인자에 expireAfterSeconds 옵션을 명시해 TTL 인덱스를 생성한다.

```
db.users.createIndex({"lastUpdated": 1}, {"expireAfterSeconds": 60*60*24})
```

lastUpdated 필드에 TTL 인덱스를 생성한다. 도큐먼트에 lastUpdated 필드가 존재하고 날짜형이라면, 서버 시간이 도큐먼트 시간의 expireAfterSeconds 초를 지나면 도큐먼트가 삭제된다.

활동 중인 세션이 삭제되는 것을 방지하려면 활동이 있을 때마다 lastUpdated 필드를 현재 시간으로 갱신한다. 몽고DB는 TTL 인덱스를 매분마다 청소하므로 초 단위로 신경 쓸 필요는 없다. collMod 명령어를 이용해 expireAfterSeconds를 변경할 수 있다.

하나의 컬렉션의 TTL 인덱스를 여러 개 가질 수 있다. 복합 인덱스는 될 수 없지만 정렬 및 쿼리 최적화가 목적이라면 일반 인덱스 처럼 사용될 수 있다.

# 7 집계 프레임워크

## 파이프라인, 단계 및 조정 가능 항목

집계 파이프라인은 배시와 같은 리눅스 셀 파이프라인과 매우 유사한 개념이며, 단계마다 특정 작업을 수행한다. 각 단계는 특정한 형태의 도큐먼트를 입력받고 특정 추력을 생성하는데, 출력은 도큐먼트 스트림이다. 파이프라인 끝에서는 find 쿼리와 거의 같은방식으로 출력에 접근한다. 집계 파이프라인의 개별 단계는 데이터 처리 단위다. 한 번에 입력 도큐먼트 스트림을 하나씩 가져와, 각 도큐먼트를 하나씩 처리하고, 출력 도큐먼트 스트림을 하나씩 생성한다.

각 단계는 knobs 또는 tunables 셋을 제공한다. 이 항목들을 조정해 각 단계를 매개변수로 지정함으로써 원하는 작업을 수행할 수 있다. 단계는 일반적이로 범용적인 작업을 수행한다. 우리는 사용중인 특정 컬렉션과 도큐머트에 수행할 작업에 따라 단계를 매개변수화한다.

파이프라인은 단계로 구성되며 각 단계는 입력에 서로 다른 데이터 처리 작업을 수행하고, 출력으로 도큐먼트를 생성해 다음 단계로 전달한다. 처리 작업 끝에서 생성한 출력은 애플리케이션 작업하는데 사용하거나, 컬렉션에 보내 나중에 사용한다.

## 단계 시작하기: 익숙한 작업들

일치, 선출, 정렬, 건너 뛰기, 제한 단계를 살펴보자.

2004년에 설립된 회사를 모두 찾는 필터는 다음과 같다.

```
db.compaies.aggregate([
  {$match: {founded_year: 2004}}
])
```

이는 find를 사용하는 다음 작업과 동일하다.

```
db.compaies.find({founded_year: 2004})
```

파이프라인에 선출 단계를 추가해 도큐먼트당 몇 개의 필드만 나타내도록 출력을 줄일 수 있다.

```
db.compaies.aggregate([
  {$match: {founded: 2004}},
  {$project: {
    _id: 0,
    name: 1,
    founded_year: 1
  }}
])
```

aggregate는 집계 쿼리를 실행할 때 호출하는 메서드다. 집계를 위해 집계 파이프라인을 전달하는데, 파이프라인은 도큐먼트를 요소로 포함하는 배열이다. 각 도큐먼트는 특정 단계 연산자를 규졍해야 한다. 예제 파이프라인은 두 단계를 포함한다. 필터링을 위한 일치 단계와, 출력을 도큐먼트당 두 개 필드로 제한하는 선출단게가 있다.

일치 단계는 컬렉션에 대해 필터링하고 결과 도큐먼트를 한 번에 하나씩 선출 단계로 전달한다. 선출 단계 작업을 수행하고 도큐먼트 모양을 변경한 후 출력을 파이프라인에서 다시 우리에게 전달한다.

결과 셋을 5개로 제한한 후 원하는 필드를 선축하는 쿼리는 다음과 같다.

```
db.compaies.aggregate([
  {$match: {founded: 2004}},
  {$limit: 5},
  {$project: {
    _id: 0,
    name: 1,
  }}
])
```

제한을 선출 단계 이전에 수행하도록 파이프라인을 구축했다는 점에 주목하자. 다음 쿼리처럼 선출 단계를 먼저 실행한 후 제한을 실행해도 동일한 결과를 얻는다. 하지만 이 경우 최종적으로 결과를 5개로 제한하기 전에 선툴 단계를 통해 수백 개의 도큐먼트를 전달해야 한다. 주어진 몽고DB 버전에서 쿼리 플래너가 수행할 수 있는 최저화 유형에 관계 없이 항상 집계 파이프라인의 효율성을 고려 해야한다. 따라서 두 번째 단계로 제란하는 것이 좋다. 하지만 순서가 중요하다면 제한 단계 전에 정렬을 수행해야한다. 정렬을 하기 위해서는 도큐먼트에 대해서 조사가 필요하다.

## 표현식

* 불리언 표현식: AND, OR, NOT 표현식
* 집한 표현식: 2개 이상의 집합의 교집합, 합집합, 차집합 등 집합에 대한 연산
* 비교 표현식: 유형의 범위 필터
* 산술 표현식: 자연 로그, 로그, 곱하기, 나누기, 더하기, 빼기와 같은 산술 연산
* 문자열 표현식: 연결, 하위 문자열, 대소문자 및 텍스트 검색과 관련된 연산
* 배열 표현식: 배열 요소를 필터링하거나, 배열을 분할하거나, 특정 배열에서 값의 범위를 가져오는 등 배열을 조적하는 연산
* 가변식 표현식: 날짜 값 구분 분석을 위한식, 조건식을 사용
* 누산기: 합계, 기술 토계 및 기타 유형의 값을 계산하는 기능을 제공

## SQL to Aggregation Mapping Chart

| SQL Terms, Functions, and Concepts | MongoDB Aggregation Operators                  |
|------------------------------------|------------------------------------------------|
| WHERE                              | $match                                         |
| GROUP                              | BY $group                                      |
| HAVING                             | $match                                         |
| SELECT                             | $project                                       |
| ORDER                              | BY $sort                                       |
| LIMIT                              | $limit                                         |
| SUM()                              | $sum                                           |
| COUNT()                            | $sum <br> $sortByCount                         |
| join                               | $lookup                                        |
| SELECT INTO NEW_TABLE              | $out                                           |
| MERGE INTO TABLE                   | $merge (Available starting in MongoDB 4.2)     |
| UNION ALL                          | $unionWith (Available starting in MongoDB 4.4) |

> [SQL to Aggregation Mapping Chart](https://www.mongodb.com/docs/manual/reference/sql-aggregation-comparison/)

## $project

```json
{
  "_id": 1,
  "title": "abc123",
  "author": {
    "last": "zzz",
    "first": "aaa"
  }
}
```

```
db.books.aggregate(
  [
    {
      $project : {
        _id: 0,
        title : 1 ,
        author : 1
      }
    }
  ]
)
```
* _id: 0 으로 제외
* title : 1 으로 포함

```
db.books.aggregate(
  [
    {
      $project : {
        "author.first" : 0,
        "lastModified" : 0
        }
      }
    ]
)
```
* 내부 컬렉션 제외

```spring-mongodb-json
db.books.aggregate(
  [
   {
      $project: {
         title: 1,
         "author.first": 1,
         "author.last" : 1,
         "author.middle": {
            $cond: {
               if: { $eq: [ "", "$author.middle" ] },
               then: "$$REMOVE",
               else: "$author.middle"
            }
         }
      }
   }
  ]
)
```
* middle가 비어 있는 경우 제외 (REMOVE)
* 로직적으로 제어 가능

```json
[
  {
    "_id": 2,
    "author": {
      "last": "xyz",
      "first": "abc",
      "middle": ""
    },
    "copies": 2,
    "isbn": "9999999999999",
    "lastModified": "2017-07-21",
    "title": "Baked Goods"
  },
  {
    "_id": 3,
    "author": {
      "last": "xyz",
      "first": "abc",
      "middle": "mmm"
    },
    "copies": 5,
    "isbn": "8888888888888",
    "lastModified": "2017-07-22",
    "title": "Ice Cream Cakes"
  },
  {
    "_id": 1,
    "author": {
      "last": "zzz",
      "first": "aaa"
    },
    "copies": 5,
    "isbn": "0001122223334",
    "lastModified": "2016-07-28",
    "title": "abc123"
  }
]
```
```
db.books.aggregate(
   [
      {
         $project: {
            title: 1,
            isbn: {
               prefix: { $substr: [ "$isbn", 0, 3 ] },
               group: { $substr: [ "$isbn", 3, 2 ] },
               publisher: { $substr: [ "$isbn", 5, 4 ] },
               title: { $substr: [ "$isbn", 9, 3 ] },
               checkDigit: { $substr: [ "$isbn", 12, 1] }
            },
            lastName: "$author.last",
            copiesSold: "$copies"
         }
      }
   ]
)
```

```json
[
  {
    "_id": 2,
    "copiesSold": 2,
    "isbn": {
      "prefix": "999",
      "group": "99",
      "publisher": "9999",
      "title": "999",
      "checkDigit": "9"
    },
    "lastName": "xyz",
    "title": "Baked Goods"
  },
  {
    "_id": 3,
    "copiesSold": 5,
    "isbn": {
      "prefix": "888",
      "group": "88",
      "publisher": "8888",
      "title": "888",
      "checkDigit": "8"
    },
    "lastName": "xyz",
    "title": "Ice Cream Cakes"
  },
  {
    "_id": 1,
    "copiesSold": 5,
    "isbn": {
      "prefix": "000",
      "group": "11",
      "publisher": "2222",
      "title": "333",
      "checkDigit": "4"
    },
    "lastName": "zzz",
    "title": "abc123"
  }
]
```

```json
[
  {
    "_id": 1,
    "stop": {
      "title": "book1",
      "author": "xyz",
      "page": 32
    },
    "user": "1234"
  },
  {
    "_id": 2,
    "stop": [
      {
        "title": "book2",
        "author": "abc",
        "page": 5
      },
      {
        "title": "book3",
        "author": "ijk",
        "page": 100
      }
    ],
    "user": "7890"
  }
]
```
* stop을 통해서 title이 포함된 무넛 필드만 출력

```
db.bookmarks.aggregate(
  [
    {$project: {"stop.title": 1}}
  ]
)
```

## $unwind

입력 문서에서 배열 필드를 분해하여 각 요소에 대한 문서를 출력합니다. 각 출력 문서는 배열 필드의 값이 요소로 대체된 입력 문서입니다.


# 8 트랜잭션

## 트랜잭션 사용법

몽고DB는 트랜잭션을 위한 두 가지 API를 제공한다. 첫 번째는 코어 API, 두 번째는 트랜잭션 사용에 권자되는 콜백 API이다. 코어 API는 대부분의 오류에 재시도 로직을 제공하지 않으며 개발자가 작업에 대한 로직 트랜잭션커밋 함수, 필요한 재시도 및 오류 로직을 모두 작성 해야한다. 콜백 API는 지정된 논리 세션과 관련된 트랜잭션 시작, 콜백 함수로 제공된 함수 실행, 트랜잭션 커밋을 포함하여 코어 API에 비해 맣은 기능을 래핑 하는 단일 함수를 재공한다. 이 함수는 커밋 오류를 처리하는 재시도 로직도 포함한다.


# 9 애플리케이션 설계

## 스키마 설계 고려 사항

### 제약 사항
도큐먼트 최대 크기는 16메가바티으이며, 디스크에서 전체 도큐먼트를 읽고 쓴다. 갱신은 전체 도큐먼츠를 다시 쓰며, 원자성 갱신은 도큐먼트 단위로 실행된다.


### 스키마 설계 패턴

#### 다형성 패턴
다형성 패턴은 컬렉션 내 모든 도큐먼트가 유사하지만 동일하지 않은 구조를 가질 때 적합하다. 공통 쿼리를 지원하는 도큐먼트에서 공통 필드를 식별하는 것이 포함된다. 도큐먼트나 서브도큐먼트의 특정 필드를 추적하면, 이러한 차이점을 고ㅓㄴ리하기 위해 애플리케이션에서 코딩할 수 있는 데이터와 다른 코드 경로 또는 클래스/서브클래스 간의 차이점을 식별하는데 도움이 된다. 이를 통해 동일하지 않은 도큐먼트로 구성된 단일 컬렉션에서 간단하기 쿼리를 사용해 쿠러 성능을 향상 시킬 수 있다.

#### 속성 패턴
속성 패턴은 정렬하거나 쿼리하려는 도큐먼트에 필드의 서브셋이 있는 경우, 정렬하려는 필드가 도큐먼트의 서브셋에만 존재하는 경우 또는 두 조건이 모두 해당되는경우 적합하다. 데이터를 키/값 쌍 배열로 재구성하고 배열요소에 인덱스를 만드는 작업이 포함된다.

#### 버킷 패턴
버킷 패턴은 데이터가 일정기간 도안 스트림으로 유입되는 시계열 데이터 적합하다. 데이터를 특정 시간범위의 데이터를 각각 보유하는 도큐먼트 셋으로 버킷화하면 시간/데이터 포인트의 포인트당 도큐먼트를 만들 때보다 훨씬 효율적이다. 예를 들어 1시간 버킷 사용해 해당 시간 동안 모든 판도 값을 단일 도큐먼트 내 배열에 배치할 수 있다.

#### 이상치 패턴
이상치 패턴은 드물게 도큐먼트의 쿼리가 애플리케이션의 정상적인 패턴을 벗어 날 때 사용한다. 인기도가 중요한 상황을 위해 설계된 고급 스키마 패턴으로, 주요 영향 요인, 도서 판매, 영화 리뷰 등이 있는 소셜에서 볼 수 있다.

#### 계산된 패턴
계산된 패턴은 데이터를 자주 계산해야할 때 데이터 접근 패턴이 읽기 집약적 일때 사용한다. 이 패턴은 주요 도큐먼트가 주기적으로 갱신되는 백그라운드에서 계산을 수행하도록 권장한다. 이는 개별 쿼리에 대해 필드나 도큐먼트를 지속적으로 생성하지 않고도 계산된 필드 및 도큐먼트의 휴효한 근사치를 제공한다. 

#### 서브셋 패턴
서브셋 패턴은 장비의 램 용량을 초과하는 작업 셋이 있을 때사용 한다. 이는 애플리케이션에서 사용하지 않는 정보를 많이 포함되는 대용량 도큐먼트 때문에 발생할 수 있다. 서브셋 패턴은 자주 사용하는 데이터와 자주 사용하지 않은 데이터를 두 개의 개별 컬렉션으로 분할하도록한다.

#### 확정된 참조 패턴
확정된 참조 패턴은 각 고유한 컬렉션이 있는 여러 논리 엔티티 또는 사물이 있고 특정 기능을 위해 엔티티를 모을 때 사용한다. 일반적인 커머스 스미카에서는 주문, 고객, 재고에 대한 별도의 컬렉션이 있을 수 있다. 그런데 개별 컬렉션에서 단일 주문에 대한 정보를 모두 수집하면 성능적인 문제가 있을 수 있다. 이때 자주 접근하는 필드를 식별하고 주문 도큐먼트로 복제하면 문제를 해결할 스 있다.

#### 근사 패턴
근사 패턴 리소스가 만힝 드는 (CPU, 메모리, 시간) 계산이 필요하지만 높은 정확도가 반드시 필요하지 않은 상황에 유용하다. 이미지나 게시글의 추천 수 카운터 또는 페이지 조회 수 카운트로 예를 들 수 있다. 소셜 미디어 좋아요 100K 이런 예

#### 트리 패턴
트리 패턴

#### 사전 할당 패턴
사전 할당 패턴

#### 도큐먼트 버전 관리 패턴
도큐먼트 버전 관리 패턴은 도큐먼트의 이전 버전을 유지하는 메커니즘을 제공한다. 메인 컬렉션의 도큐먼트 버전을 추적하려면 각 도큐ㅜ먼트에 부가 필드를 추가해야하며 도큐먼트의 모든 수정 사항을 포함하는 추가 컬렉션이 필요하다.

## 정규화 vs 비정규화

| 내장 방식이 좋은 경우               | 참조 방식이 좋은 경우     |
|----------------------------|------------------|
| 작은 서브도큐먼트                  | 큰 서브도큐먼트         |
| 주기적으로 변하지 않은 데이터           | 자주 변하는 데이터       |
| 결과적인 일관성이 허용될 때            | 즉각적인 일관성이 필요할 때  |
| 증가량이 적은 도큐먼트               | 증가량이 많은 도큐먼트     |
| 두 번째 쿼리를 수행하는 데 자주 필요한 데이터 | 결과에서 자주 제외되는 데이터 |
| 빠른 읽기                      | 빠른 쓰기            |



# 10 복제 셋 설정


## 복제 소개
복제 셋은 클라이언트 요청을 처리하는 프라이머 서버 한 대와, 프라이머리 데이터의 사본을 갖는 세컨터더 서버 여러 대로 이루어진다. 프라이머리 서버에 장애가 발생하면 세컨더리 서버는 자신들 중에서 새로운 프라이머리 서버를 선축할 수 있다.

복제를 사용하는 상태에서 서버가 다운되면, 복제 셋에 있는 다른 서버를 통해 데이터에 접근할 수 있다. 서버상의 데이터가 손상되거나 접근할 수없는 생태라면 복제 셋의 다른 멤버로부터 새로운 복제 데이터를 만들 수 있다.

## 복제 셋 설정 1장

## 네트워크 고려 사항
복제 셋의 머든 멤버는 같은 셋 내 다른 멤버와 연결할 수 있어야 한다. 자기 자신을 포함해서 만약 이미 작동 중인 다른 멤버에 연결할 수 없다는 오류가 발생하면. 연결이 이뤄지도록 네트워크 구성을 바꿔야한다.


## 복제 셋 설정 2장

## 복제 셋 설계 방법
프라이머리를 선출하려면 멤버의 과반수 이상이 필요하고, 프라이머리는 과반수 이상이어야만 프라이머리 자격을 유지할 수 있다.

### 어떻게 선출하는가
* 요청받은 멤버가 프라이머리에 도달할 수 있는가?
* 선출되고자 하는 멤버의 복제 데이터가 최신인가?
* 대신 선출돼야 하는 우선순위가 더 높은 멤버가 없는가 ?


# 14 샤딩이란

샤딩은 여러 장비에 걸쳐 데이터를 분할하는 과정을 일컫으며, 때때로 파티셔닝 이라는 용도로 불린다. 몽고DB는 자동 샤딩을 지원한다.

# 15 샤딩 구성

## 언제 샤딩 해야 하나

* 사용 가능한 메모리를 늘릴 때
* 사용 가능한 디스크 공간을 늘릴 때
* 서버의 부하를 줄일 때
* 한 개의 mongod가 다룰 수 있는 처리량보다 더 많이 데이터를 읽거나 쓸 때

### 데이터 샤닝

몽고DB는 데이터를 어떨게 분산할지 아렬주지 전에는 자동으로 데이터를 분산하지 않는다. 분산하려는 데이터베이스와 컬렉션을 명시적으로 알려줘야한다. 데이터베이스의 샤딩을 활성화 해야 한다. 컬렉션을 샤딩 하려면 트겆ㅇ 필드에 인덱스가 있어야 한다.

기존 컬렉션을 샤딩하는 경우청크로 나누고 데이터를 옮기 시작한다. 명령 실행이 성공하면 몽고DB는 클러스터의 샤드에 컬렉션을 분산한다. 이 프로세스는 즉시 끝나지 않으며, 컬렉션이 크면 최초 분산을 끝내는 데 몇시간이 걸릴 수도 있다.

## 몽고DB는 어떻게 클러스터 데이터를 추적하는가

도큐먼트를 어디서 찾을지 항상 알아야 한다. 이론상 몽고DB는 각각의 도큐먼트가 어디 있는지 추적할 수 있지만, 도큐먼트가 수백 수억개인 컬렉션은 다루기 어렵다. 그러므로 몽고DB는 주어진 샤드 키 범위 내에 있는 도큐먼트를 청크로 나눈다. 

예를 들어 샤드키가 {"age": 1} 이면, 하나의 청크는 "age" 필드가 3, 17사이인 모든 도큐먼트가 된다. {"age": 5} 쿼리 요청을 받으면 청크 가 있는 샤드에 쿼리를 라우팅 한다.

청크가 일정 크키까지 커지면 몽고DB는 자동으로 두개의 작은 청크로 나눈다. 3~11 까지 도큐먼트를 포함하는 청크와 12세부터 17세까지 도큐먼트를 포함하는 청크로 나뉠 수 있다.

### 청크 범위

age가 3과 17 사이인 모든 도큐먼트는 하나의 청크, 즉 3 <= "age" < 17에 포함된다. 이를 나누면 두 개의 범위가 되며, 한 청크는 3 <= "age" < 12, 12 <= "age" < 17 이다. 이때 12는 분할점 이라 불린다.


### 청크 분할

샤드 키에 대해 다양한 값을 갖는 것이 중요하다.

## 밸런서

밸런서는 데이터 이동을 책임진다. 주기적으로 샤드 간의 불군형을  체크하다가 불군현이면 청크를 이동하기 시작한다. 

## 콜레이션

콜레이션을 이용해 문자열 비교를 위한 언어별 규칙을 지정할 수 있다. 규칙의 예로 대소문자와 강제 부호를 비교하는 방법이 있다.

## 스트림변경

스트림 변경을 사용하면 애플리케이션 데이터베이스 내 데이터의 실시간 변경 사항을 추적할 수 있다.

# 16 샤드 키 선정

컬렉션을 샤딩할 때는 데이터 분할에 사용할 한 두 개의 필드를 선태갛ㄴ다. 이 키를 샤드 키라고 한다. 컬렉션을 샤딩하고 ㅏ넛는 샤드 키를 변경할 수 없으므로 올바르게 선택 하는 것이 중요하다.


* 샤드가 얼마나 많이 늘릴 것인가?
  * 샤드가 세 개인 클러스터는 샤드가 천 개인 클러스터보다 훨씬 유연하다. 클러스터가 점점 더 커질 때, 쿼리가 모든 샤드를 방문 해야하는 쿼리를 피하려면 거의 모든 쿼리가 샤드 키를 포함해야 한다.
* 읽기 혹은 쓰기 응답 대기 시간을 줄이려고 샤딩하는가?
  * 응답 대기 시간은 무언가를 얻을 데 걸리는 시간을 일컫는다. 예를 들어 쓰기 20밀리초가 걸리지만 10밀리초로 줄일 필요가 있다.
  * 쓰기 응답 대기 시간을 줄이는 일은 일반적으로 요청을 지리적으로 더 가까운 곳이나 더 좋은 방비로 보내는 작업과 관련 있다.
* 읽기 혹은 쓰기 처리양을 늘릴려고 샤딩하는가?
  * 처리량을 늘리는 일은, 더 많은 병렬 처리 기능을 추가하고 클러스터에 요청을 균일하게 분산하는 작업과 관련 있다.
* 시스템 리스소를 늘릴려고 샤딩하는가
  * 데이터의 크기가 1기가 바이트당 몽고DB에 더 많은 메모리를 제공하려고 하는가? 만약 그렇다면 작업 셋의 크기를 가능한 적게 우지할 필요가 있다.


## 샤딩 구상

### 오름차순 키

오름차순 키는 일반적으로 date, ObjectId 처럼 시간에 따라 구준히 증가하는 것이면 무엇이든 된다. 여러 데이터가 들어오는 경우 MinKey, MaxKey를 지정하여 균등하게 샤드를 나눠서 저장하려고 한다. 하지만 몽고DB가 청크를 균형 잡힌 상태로 유지하기 어렵게 할 때가 많다. 모든 청크가 하나의 샤드에서 생성되기 때문이다. 그러므로 몽고DB 작은 불균형을 바로잡을게 아니라, 지속적으로 청크를 다른 샤드로 옮겨야 한다.

4.2 부터는 autosplit 기능으로 오름차순 샤드 키 패턴에 대해서 최상위 청크 최적화가 추가됐다. 밸런서는 최상위 청크를 배치할 다른 샤드를 선태가한ㄷ. 이는 새 청크가 모두 단 하나의 샤드에 생성되는 상황을 방지한다.


### 무작위 분산 샤크 키

사용자명, 이메일 주소, UUID 같은 공유하지 않은 키는 모두 무작위 분산 샤드 키가 될 수 있다. 무산위성은 모든 청크에 고르게 이루어쟈야 함을 의미한다. 만 개의 도큐먼트를 입력한 후 확인 해보면 거의 비슷한 비율로 샤드가 분산된다.

무산위 분산 샤드키의 유일한 단점은, 몽고DB가 메모리 크기를 넘어서는 데이터를 임의로 접근하는 데 비효율적이라는 것이다. 하지만 가용 메모리가 있거나 성능이 저하돼도 상관 없다면 무작위 키는 클러스터에 부하를 분한하는 훌륭한 방법이다.

### 위치 기반 샤드 키

우치 기반 샤드 키는 사용자 IP, 경도와 위도, 주소 등이 될 수 있다. 반드시 물리적 위치 필드와 관련될 피룡는 없으며, 위치 데이터를 그룹화하는 추상적인 방법이다. 어떤 경우든 위치 기반 키는, 어떤 유사성을 갖는 도큐먼트가 해당 필드 기반의 범위에 포함되는 키다.

## 샤드 키 전략

### 해시 샤드 키 
데이터를 가능한 빠르게 로드하려면 해시 샤드 키가 최선의 선택이다. 


## 샤드 키 규칙 및 지침

샤드 키를 결정하고 생성하는 작업 은 인덱싱과 개념이 비슷하다. 샤드 키는 가장 자주 사용하는 키로 변형된 인덱스로 볼 수 있다.

### 샤드 키 한계

* 샤드키는 배열이 될 수 없다.
* 도큐먼트 샤으 키 값은 입력 후 수정할 수 있다. 4.2 이전 버전에서는 샤드 키를 수정할 수 없다

#### 샤드 키 카디널리티

샤드 키가 급력하게 증가하든 꾸준히 증가하든 상관 없이, 고르게 입력될 값으로 키를 선택 해야한다. "DEBUG", "WARN", "ERROR" 뿐인 Log Level 키가 있다면 몽고DB는 데이터를 세 개 이상의 청크로 쪼갤 수 없다. 변화가 거긔 없는 키를 샤드 키로 사용하려면 "Log Level", "timestamp" 처럼 다양성이 있는 키와 복합 샤드 키를 생성해서 이용하자.


## 데이터 분산 제어


### 다중 데이터베이스와 컬렉션을 위한 클러스터 사용

데이터의 가치가 비지니스 적으로 작은 가치에 해당 하는 경우(로그 데이터 등) 비싼 서버에서 공간을 차지하지 않기를 원할 수 도 있다. 이런 구체적인 지시를 내릴 수 있다.

### 수동 샤딩

때대로 요구 사항이 복잡할 때나 특수한 상황에서는 데이터를 분산할 위치를 완전히 제어하고 싶을 수 있다. 데이터가 저장으로 분산되지 않도록 밸런서를 끄고, moveChunk 명령으로 수동으로 데이터를 분산하자


# 17 샤딩 관리

## 현재 상태 확인

데이터의 위치, 샤드 구성, 클러스터 실행 작업을 조회할 수 있는 몇가지 보조자가 있다. 



# 22 몽고 DB 모니터링

## 메모리 사용 모니터링

* 상주 메모리
* 가상 메모리
* 대응 메모리


데이터가 완전히 메모리에 들어가는 경우 상주 메모리 크기는 데이터 크기와 비슷해야 한다. 데이터가 메모리 내에 있다는 말은 데이터가 렘에 있음을 의미한다.


### 페이지 폴트 추적

다른 통계를 사용해서 몽고DB가 메모리를 얼마나 사용하고 있는지 알 수 있지만, 각 유형의 메모리가 얼마나 사용되는지는 알 수 없다. 유용한 통계인 페이지 폴트 횟수는, 몽고DB에서 찾는 데이ㅓ가 얼마나 자주 램에 존재하지 않는지 알려준다.


### 입출력 대기

페이지 폴트는 일반적으로 CPU가 디스크를 기다리면서 얼마나 오랫동안 유효 상태 인지와 밀접하게 관련되며, 이를 입출력 대기리 부른다.

### 작업 셋 계산

일반적으로 메모리에 데이터가 많을수록 몽고DB는 연산을 더 빠르게 수행한다.


1. 메모리상의 전체 데이터셋, 보유하면 좋지만 종종 너무 비싸거나 실행이 불가능하다, 응답 시간이 빨라야 하는 애플리케이션에서는 필수다.
2. 메모리상의 작업 셋, 가장 일반적이다.
3. 메모리상의 인덱스
4. 메모리상의 인덱스의 작업 셋
5. 메모리상의 유용하지 않은 데이터의 서브셋, 시스템이 느려지게 하므로 가능한 피하자

## 성능 추적


# 스터디

* 일반적인 집계와 달리 파이프라인을 구성하여 각 단계를 넘기는 방식
* 집계함수 limit