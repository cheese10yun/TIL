# MySQL Concurrency

## 쓰기락과 읽기락

* 락을 통해 동시성을 제어할 때는, 락의 범위를 최소화
* MySQL에서는 트랜잭션의 커밋 혹은 롤백시점에서 잠금이 풀린다. 트랜잭션이 곧 락의 범위, 락에 범위를 최소화 한다는 것은 트랜잭션 범위를 최소화 하는 것으로 볼 수 있다
* MySQL에서는 쓰기락, 읽기락 두 가지 잠금을 제공

## 쓰기락과 읽기락

|                     | 읽기락(Shared Lock) | 쓰기락(Exclusive Lock) |
|---------------------|------------------|---------------------|
| 읽기락(Shared Lock)    | O                | 대기                  |
| 쓰기락(Exclusive Lock) | 대기               | 대기                  |

* 읽기락(Shared Lock)이 잠금을 획득한 상태에서 다른 읽기락(Shared Lock)이 들어오면 이름 처럼 공유가 가능
* 쓰기락(Exclusive Lock) 베타적인 락이기 떄문에 읽기락이 걸려 있든 쓰기락이 걸려있든 무조건 대기가 필요
* 읽기락은 SELECT ... FOR SHARE 쿼리
* 쓰기락은 SELECT ... FOR UPDATE, UPDATE, DELETE 쿼리
* **매번 잠금이 필요한 경우, 성능저하를 피할 수 없기 떄문에 MySQL에서 일반적인 SELECT는 Nonblocking Consistent Read로 동작**
* SELECT는 Nonblocking Consistent Read 동작이 가능한 이유는 MVCC를 통한 UNDO Log를 통해 가능하다.

### 레코드 락

* 대부분 락을 사용하면 레코드 락을 사용한다.
* MySQL에서 잠금은 row가 아니라 인덱스를 잠근다. 인덱스가 없는 조건으로 Locking Read시 불필요한 데이터들이 잠길 수 있음.
* MySQL의 중요한 특징중 하나로 인덱스가 중요한 이유이다. 락을 잡을 때도 인덱스에 따라서 락이 잡히는 범위가 달라지기 때문에 중요
  * 인덱스가 없는 조건으로 where 문으로 락을 잡는 경우 불필요한 데이터가 잠길 수 있다.

### 실습

| id | member\_id | content |
|:---|:-----------|:--------|
| 1  | 1          | 1111    |
| 2  | 1          | 222     |
| 3  | 1          | 333     |
| 4  | 1          | 444     |
| 5  | 1          | 555     |
| 6  | 1          | 555     |

* member_id 인덱스가 걸려있음

```
-- 커넥션 A 
start transaction;
select *from post where member_id = 1 and content = '555' for update;
# commit ; -- commit 진행하지 않음 
----

-- 커넥션 B, 커넥션 A가 Lock 쓰기락을 걸고 있음
start transaction;
select *from post
where member_id = 1 and content = '222' for update ;
commit
```

* member_id 으로 조회하고 있으며, member_id는 인덱스키 이기 때문에 레코드 락이 발생하게 된다.
* 이렇게 레코드 락이 발생하기 때문에 커넥션B의 응답은 커넥션 A의 commit으로 트랜잭션을 종료하기 전까지 응답받을 수 없다.

```sql
select *
from information_schema.innodb_trx;
select *
from information_schema.INNODB_LOCKS;
```
