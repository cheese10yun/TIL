> [빅데이터 저장 및 분석을 위한 NoSQL & Redis](http://m.yes24.com/goods/detail/71131862) 내용 정리

# 2 Redis 설치 및 데이터 처리

```
$ docker pull redis
$ docker run --name redis-study -d -p 6379:6379 redis
$ docker run -it --link redis-study:redis --rm redis redis-cli -h redis -p 6379
```


## 데이터 처리

### 용어 설명

![](../assets/redis-desc.jpg)

용어 | 설명
---|---
Table | 하나의 DB에사 데이터를 저장하는 논리적인 구조
Data Sets | 테이블을 구성하는 논리적 단위입니다. 하나의 데이터-셋은 하나의 KEY와 한 개 이상의 `FIELD/ELEMENT`로 구성됩니다.
Key | 하나의 KEY는 하나 이상의 조합된 값으로 표현 가능합니다. 예를들어 주문번호 또는 주문번호+순번, 년월일+순번
Values | 해당 Key에 대한 구체적인 데이터를 값으로 펴햔합니다. 벨류는 하나 이상의 `Field, Element`로 구성됩니다.

### 데이터 입력/수정/삭제/조회

종류 | 내용
---|---
set | 데이터를 저장할떄(key, value)
get | 저장된 데이터를 검색할 때
rename | 저장된 데이터 값을 변경할 때
randomKey | 지정된 Key 중에 하나의 Key를 랜덤하게 검색할 때
Keys | 지정된 모든 Key를 검색할 때
exits | 검색 대상 Key가 존재하는지 여부를 확인할 때
mset/mget | 여러 개의 Key와 Value를 한번 저장하고 검색할 때

Redis DB에 데이터를 입력/수정/삭제/조회 하기 위해서는 반드시 Redis 서버에서 제공하는 명령어를 사용해야 하며 데이터를 조장할 떄는 Key에 대한 하나 이상의 Field 또는 Element로 표현해야합니다.


### 실습

```
redis:6379> set 1111 "Yun" # 해당 키값으로 벨류 저장
redis:6379> get 1111 # 해당 키에대한 데이터 검색
redis:6379> keys * # 저장되어 있는 모든 key 출력
redis:6379> keys *2 # 저장되어 있는 key 중에 2로 끝나는 key 검색 
redis:6379> rename 1111 1112 # key 1111 -> 1112 변경
redis:6379> rename 1111 1112 # key 1111 -> 1112 변경
redis:6379> randomkey # 현재 key중에서 랜덤 key 검색
redis:6379> exists 1116 # 키존재 여부 존재하는 경우 1
redis:6379> strlen 1111 # 해당 키 값의 value 길이길이
redis:6379> flushall # 저장되어 있는 모든 key 삭제
redis:6379> setex 123 30 "asd" # 일정시간 동안만 저장
redis:6379> ttl 123 # 현재 남아있는 시간
redis:6379> mset 111 "111" 123 "123" # 여러 개 필드를 한 번에 저장
redis:6379> mget 111 123 # Mset에 저장된 값을 한번에 다중 검색
1) "111"
2) "123"
redis:6379> mget 111 123 11 noneexisting # 존재하지 않는 경우 nil 출력
1) "111"
2) "123"
3) (nil)
4) (nil)
redis:6379> append 111 "asdasd" # 현재 value에 value 추가
(integer) 9
redis:6379> get 111
"111asdasd"
redis:6379> save # 현재 입력한 key/value 값을 파일로 저장
OK
redis:6379> info # Redis 서버 설정 상태 조회
# Server
redis_version:6.2.1
...

```

### 데이터 타입

종류 | 내용
---|---
strings | 문자, Binary 유형 데이터를 저장
List | 하나의 Key에 여러 개의 배열 값을 저장
Hash | 하나의 Key에 여러 개의 Fields와 Value로 구성된 테이블을 저장
Set / Sorted set | 정령되지 않은 String 타입 / Set과 Hash를 결합한 타입
Bitmaps | `0,1`로 표현하는 데이터 타입
HyperLogLogs | Element 중에 Unique 한 개수의 Element 만 계산
Geospatial | 좌표 데이터를 저장 및 관리하는 데이터 타입


#### Hash 타입

![](../assets/redis-hash.jpg)

하나의 Key와 하나 이상의 `Field/Element` 값으로 저장할 수 있습니다. Value에는 기본적으로 Strings 데이터를 저장할 수 있으며 추가로 컨테이너 타입의 데이터들을 저장할 수 있습니다.

타입의 데이터들은 저장할 수 있습니다. 컨테이너 타입에는 Hash, List, Set/Sorted Set 4가지 유형이 있습니다. 다음 설명은 Hash에 대한 설명입니다.

* Hash 타입은 기존 관계형 DB에서 Primary-key와 하나 이상의 커럶으로 구성된 테이블 구조와 매우 흡사한 데이터 유형입니다.
* 하나의 Key는 오브젝트명과 하나 이상의 필드 값을 콜론 `:` 기호로 결합하여 표현할 수 있습니다. ex order:201809123, order_detial:201809123:01
* 문자 값을 저장할 떄는 잉용부호를 `""`를 사용하여 숫자 값을 저장할 떄는 잉용부호가 필요하지 않습니다
* 기본적으로 필드 개수는 제한 없습니다.
* Hash 타입의 데이터를 처리할 때는 hmset, hget, hgetall, hkey, hlen 명령어를 사용합니다.

#### 실습

![](../assets/redis-hash-2.jpg)