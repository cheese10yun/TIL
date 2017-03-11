# Redis 입문

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
