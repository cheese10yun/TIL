## Redis 입문

## 목차
- [Redis 입문](#redis-%EC%9E%85%EB%AC%B8)
- [목차](#%EB%AA%A9%EC%B0%A8)
- [여러개 한번에 입력](#%EC%97%AC%EB%9F%AC%EA%B0%9C-%ED%95%9C%EB%B2%88%EC%97%90-%EC%9E%85%EB%A0%A5)
- [연산](#%EC%97%B0%EC%82%B0)
- [문자열 더하기](#%EB%AC%B8%EC%9E%90%EC%97%B4-%EB%8D%94%ED%95%98%EA%B8%B0)
- [length, 맴버 제거](#length-%EB%A7%B4%EB%B2%84-%EC%A0%9C%EA%B1%B0)
- [판단](#%ED%8C%90%EB%8B%A8)
- [집합](#%EC%A7%91%ED%95%A9)
    - [합집합](#%ED%95%A9%EC%A7%91%ED%95%A9)
    - [교집합](#%EA%B5%90%EC%A7%91%ED%95%A9)
    - [차집합](#%EC%B0%A8%EC%A7%91%ED%95%A9)
- [sort](#sort)
- [rank](#rank)
- [Hash Set](#hash-set)
- [Hash Get](#hash-get)
- [Hash Dell](#hash-dell)
- [Hash 증가](#hash-%EC%A6%9D%EA%B0%80)
- [List](#list)

## 여러개 한번에 입력
```
127.0.0.1:6379> mset values1 10 values2 20 values3 30
127.0.0.1:6379> mget value1 values2 value3
1) "10"
2) "20"
3) "30"
```

## 연산
```
127.0.0.1:6379> DECRBY mykey 3
(integer) 7
127.0.0.1:6379> INCRBY mykey 3
(integer) 10
127.0.0.1:6379> DECR mykey
(integer) 9
127.0.0.1:6379> INCR mykey
(integer) 10
127.0.0.1:6379>
```

## 문자열 더하기
```
127.0.0.1:6379> sadd myset "Hello"
(integer) 1
127.0.0.1:6379> sadd myset "World"
(integer) 1
127.0.0.1:6379> sadd myset "World"
(integer) 0
127.0.0.1:6379> smembers myset
1) "World"
2) "Hello"
127.0.0.1:6379>
```

* `sadd` 문자열 더하기, `sadd`값이 없을 경우는 `set`처럼 인식
* `smembers` 해당 맴버의 모든 값들을 출력

## length, 맴버 제거
```
127.0.0.1:6379> scard myset
(integer) 2
127.0.0.1:6379> srem myset "Hello"
(integer) 1
127.0.0.1:6379> smembers myset
1) "World"
```

* `scard` length와 비슷한 기능
* `srem` 멤버에있는 값 제거
* `smembers myset` 출력시 `Hello`삭제 확인


## 판단

```
127.0.0.1:6379> sismember myset "Hello"
(integer) 0
127.0.0.1:6379> sismember myset "World"
(integer) 1
```

* `sismember` 해당 문자열이 멤버인지 판단
* `Hello` 위에서 삭제된 멤버임으로 `0` 반환, `World` 존재하는 멤버임으로 `1`반환


## 집합
```
다음과 같은 데이터가 있다는 가정
127.0.0.1:6379> sadd key1 "A"
(integer) 1
127.0.0.1:6379> sadd key1 "B"
(integer) 1
127.0.0.1:6379> sadd key1 "C"
(integer) 1
127.0.0.1:6379> sadd key2 "B"
(integer) 1
127.0.0.1:6379> sadd key2 "C"
(integer) 1
127.0.0.1:6379> sadd key2 "D"
(integer) 1
```

### 합집합
```
127.0.0.1:6379> sunion key1 key2
1) "A"
2) "B"
3) "C"
4) "D"
```
* `sunion` 합집합임으로 모든 결과 출력

### 교집합

```
127.0.0.1:6379> sinter key1 key2
1) "B"
2) "C"
```

### 차집합
```
127.0.0.1:6379> sdiff key2 key1
1) "D"
127.0.0.1:6379> sdiff key1 key2
1) "A"
```
* 순서가의 영향이 미친다.


## sort
```
127.0.0.1:6379> zadd myzset 1 "one"
(integer) 1
127.0.0.1:6379> zadd myzset 1 "uno"
(integer) 1
127.0.0.1:6379> zadd myzset 2 "two" 3 "three"
(integer) 2
127.0.0.1:6379> zrange myzset 0 -1 withscores
1) "one"
2) "1"
3) "uno"
4) "1"
5) "two"
6) "2"
7) "three"
8) "3"
127.0.0.1:6379>
```

* `zrange myzset 0 -1 withscores` 스코어에 따라서 처음부터 끝까지 출력

## rank
```
127.0.0.1:6379> zrank myzset "two"
(integer) 2
127.0.0.1:6379> zrank myzset "one"
(integer) 0
127.0.0.1:6379> zrank myzset "uno"
(integer) 1
127.0.0.1:6379> zrank myzset "one"
(integer) 0
```
* `0`부터 시작된다 프로그래밍할때 신경 써야 할부분
* 스코어가 같다고 rank가 동일하지는 않는다.

## Hash Set

```
127.0.0.1:6379> hmset myhash field1 "Hello" field2 "World"
OK
127.0.0.1:6379> hmset myhash field3 "12345"
OK
```
* `hmset` `h`는 Hash 약자, `m`는 Multiple 약자
* `field1` 값에 `Hello` 저장 이렇게 여려개 저장 가능


## Hash Get

```
127.0.0.1:6379> hget myhash field1
"Hello"
127.0.0.1:6379> hkeys myhash
1) "field1"
2) "field2"
3) "field3"
127.0.0.1:6379> hvals myhash
1) "Hello"
2) "World"
3) "12345"
```
* `hget` myhash의 특정 필드의 값가져오기
* `hkeys` 해당 필드의 리스트들 가져오기
* `hvals` myhash의 필드의 모든 값들 가져오기

## Hash Dell

```
127.0.0.1:6379> hdel myhash field1
(integer) 1
127.0.0.1:6379> hget myhash field1
(nil)
```

* 해당 필드 값제거
* 제거가 완려되면 `nil`표시

## Hash 증가

```
127.0.0.1:6379> hincrby myhash field3 2
(integer) 12347
```
* `field3`의 값 2증가

## List

```
127.0.0.1:6379> hincrby myhash field3 2
(integer) 12347
127.0.0.1:6379> lpush mylist "world"
(integer) 1
127.0.0.1:6379> lpush mylist "hello"
(integer) 2
127.0.0.1:6379> lrange mylist 0 -1
1) "hello"
2) "world"
```
