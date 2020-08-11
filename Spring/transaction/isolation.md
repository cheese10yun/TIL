> 스프링 5 레시피를 보고 정리한 내용입니다.

# 트랜잭션

트랜잭션이란 쉽게 말해 연속된 여러 액션을 한 단위의 작업으로 뭉뚱그린 겁니다. 이 액션 뭉치는 전체가 완전히 끝나든지, 아니면 아무런 영향도 미치치 않아야 합니다. 모든 액션이 제대로 잘 끝나면 트랜잭션을 영구 커밋되지만 하나라도 잘됫되면 애초에 아무 일도 없던 거처럼 초기 상태로 롤백됩니다.


## 트랜잭션 격리 속성 
이름 | 설명
---|---
오염된 읽기 Duty Read | T2가 수정후 커밋하지 않은 필드를 T1이 읽는상황에서 **나중에 T2가 롤백되면 T1이 읽은 필드를 일시적인 값으로 더이상 유효하지 않습니다.**
재현 불가한 읽기 Nonrepeatable Read | 어떤 필드를 T1이 읽은 후 T2가 수정할 경우, **T1이 같은 필드를 다시 읽으면 다른 값을 얻습니다.**
허상 읽기 Phantom Read | T1이 테이블의 로우를 몇개를 읽은 후 T2가 같은 테이블에 새 로우를 삽입할 경우, 나중에 T1이 같은 테이블을 다시 읽으면 T2가 삽입한 로우가 보입니다.
소실된 수정 Lost Update | T1, T2가 모두 어떤 로우를 수정하려고 읽고 그 로우의 상태에 따라 수정하는 경우입니다. T1이 먼저 로우를 수정 후 커밋하기 전, T2가 T1이 수정한 로우를 똑같이 수정했다면 T1이 커밋한 후에 T2 역시 커밋하게 됩니다. 그러면 **T1이 수정한 로우를 T2가 넢어쓰게 되어 T1이 수정한 내용이 소실됩니다.**


이론적으로 이런 저수준의 문제를 예방하려면 트랜잭션을 서로 완전히 격리 하면 되겠지만 그렇게 하면 트랜잭션을 한 줄로 세워놓고 하나씩 실행하는 꼴이라서 엄청난 성능 저하가 유하게 됩니다. **실무에서는 성능을 감안하여 트랜잭션 격리 수준을 낮추는 게 제일 일반적입니다.**



| 격리 수준            | 설명                                                                                                                                |
|------------------|-----------------------------------------------------------------------------------------------------------------------------------|
| DEFAULT          | DB 기본 격리 수준을 사용합니다.                                                                                                               |
| READ_UNCOMMITTED | **다른 트랜잭션이 아직 커밋하지 않은 값을 한 트랜잭션이 읽을 수 있습니다.** 따라서 오염된 읽기, 재현 불가한 읽기, 허상 읽기 문제가 발생할 가능성이 있습니다.                                         |
| READ_COMMITTED   | **한 트랜잭션이 다른 트랜잭션이 커밋한 값만 읽을수 있습니다.** 이로써 오염된 값 읽기 문제는 해결되지만 재현 불가한 읽기, 허상 읽기 문제는 여전히 남습니다.                                           |
| REPEATABLE_READ  | 트랜잭션이 어떤 필드를 여러번 읽어도 동일한 값을 읽도록 보장합니다. **트랜잭션이 지속되는 동안 다른 트랜잭션이 해당 필드를 변경할수 없습니다.** 오렴된 값 읽기, 재현 불가한 읽기 문제는 해결되지만 허상 읽기는 여전히 남습니다.    |
| SERIALIZABLE     | 트랜잭션이 테이블을 여러 번 읽어도 정확히 동일한 로우를 읽도록 보장합니다. **트랜잭션이 지속되는 동안에는 다른 트랜잭션이 해당 테이블에 삽입, 수정, 삭제를 할 수 없습니다.** 동시성 문제는 모두 해소되지만 성능이 현저히 떨어집니다. |


## Code

```java
public class JdbcBookShop extedns JdbcDaoSupport {
    
    @Transactional
    public void increaseStock(String isbn, int stock){
        String threadName = Thread.currentThread().getName();
        System.out.println(threadName + "- Prepareto increase book stock");

        getJdbcTemplate().update("update book_stock set stcok = stock +? where isbn =?", stock, isbn);

        System.out.println(threadName + "- Book stock increased by" + stock);
        sleep(threadName);

        System.out.println(threadName + "- Book stock rolled back");
        throw new RuntimeException("Increased by mistake");
    }

    @Transactional(isolation = Isolation.READ_UNCOMMITTED)
    public int checkStock(String isbn){
        String threadName = Thread.currentThread().getName();
        System.out.println(threadName + "- Prepare to check book stock");

        int stock = getJdbcTemplate().queryForObejct("select stcok from book_stock where isbn = ?", Interger.class, isbn);

        System.out.println(threadName + "- Book stock is "+ stock);
        sleep(threadName);

        return stock;
    }

    private void sleep(String threadName) {
        System.out.println(threadName + " - Sleeping");

        try {
        Thread.sleep(10000);
        } catch (InterruptedException e) {
        }

        System.out.println(threadName + " - Wake up");
    }
}
```
* 동시성을 시뮬레이션 하기 위해 여러 스레드로 실행
* SQL문이 실행될 때 마다 강제로 스레드를 10초간 sleep
* increase() 메서드는 마지막 RuntimeException을 던지면서 트랜잭션이 롤백 진행


## READ_UNCOMMITTED, READ_COMMITTED 격리 수준
READ_UNCOMMITTED는 한 트랜잭션이 다른 트랜잭션이 아직 커밋하기 전에 번경한 내용을 읽을 수 있는 가장 하위 수준의 격리 수준입니다.


```java
@Transactional(isolation = Isolation.READ_UNCOMMITTED)
```

### Test
```java
public class Main {

    public staic void main(String[] args){
        final BookShop bookShop = context.getBean(BookShop.class);

        Thread thread1 = new Thread(()->{
            try {
                bookShop.increaseStock("0001", 5);
            } catch (RuntimeException e) {}
        }, "Thread1");


        Thread thread2 = new Thread(()->{
            bookShop.checkStock("0001");
        }, "Thread2");


        thread1.start();
        try {
            Thread.sleep(5000);
        } catch (Exception e) {}

        thread2.start();

    }
}
```

```
Thread 1 Book stock 증가 준비중
Thread 1 Book stock 5 증가
Thread 1 스레드 sleep
Thread 2 Book stock 제고 확인
Thread 2 Book stock 재고 15개
Thread 2 Sleeping
Thread 1 Wake Up
Thread 1 Book stock rolled back
Thread 2 Wake Up
```


1. 스레드 1이 도서 재고를 늘리고 잠듭니다. (아직 트랜잭션 롤백 이전)
2. 스레드2 도서 재고 확인 **(격리 수준이 READ_UNCOMMITTED 이므로 스레드2는 스레드1의 트랜잭션이 아직 변경 후 커밋하지 않은 재고의 값을 읽어옴)**
3. 스레드 1이 wake up 되면서 Exception 발생 하여 롤백
4. **스레드2가 읽은 재곳값 15는 더이상 유효하지 않음** 이런 증상을 오염된 읽기 Duty Read 라고 한다.

이러한 문제는 격리 수준을 READ_COMMITTED로 한 단계 올리면 해결됩니다. 

스레드2는 스레드 1이 트랜잭션을 롤백 하기 전까지 재곳값을 읽을 수 없습니다. **오염된 읽기는 한 트랜잭션이 아직 커밋하지 않은 갓을 다른 트랜잭션이 읽지 못하게 차단함으로써 방지 할 수 있습니다.**

READ_COMMITTED 격리 수준을 지원하는 DB는 수정은 되었지만 **아직 커밋하지 않은 로우에 수정 잠금을 걸어둔 상태 입니다.** 결국 다른 트랜잭션이 이 **트랜잭션이 커밋/롤백 되고 수정 잠금이 풀릴 때 까지 기다렸다가 읽을 수 밖에 없습니다.**

## REPEATABLE_READ 격리수준

이번 에는 스레드 1이 도서 재고를 체크하고, 스레드 2가 도서 재고를 늘리는 일을 합니다.

1. 스레드 1이 재고를 체크하고 sleep (아직 트랜잭션이 커밋되지 않은 상태)
2. 스레드 2가 시작되어 도서 재고를 늘립니다. (격리수준 READ_COMMITTED 이므로 커밋되지 않은 트랜잭션이 읽은 재곳값을 수정할 수 있습니다.)
3. 스레드 1이 께어 나면 도서 재고를 다시 읽으면 그 값은 이미 처음에 읽은 그 값이 아닙니다.

이러한 문제를 재현 불가한 읽기 문제이며 **한 트랜잭션이 동일한 필드를 다른 값으로 읽어들이는 모순이 발생합니다.** 해결 방법은 REPEATABLE_READ으로 격리 수준을 변경하면 됩니다.

1. 스레드 1이 재고를 체크하고 sleep (아직 트랜잭션이 커밋되지 않은 상태)
2. 스레드 2는 스레드 1이 트랜잭션을 **커밋하기 전까지 재곳값을 수정할 수 없습니다.**

**REPEATABLE_READ 격리 수준을 지원하는 DB는 조회는 되었지만 아직 커밋하지 않은 로우에 읽기금을 걸어둔 상태입니다. 결국 다른 트랜잭션은 이 트랜잭션이 커밋/롤백하여 읽기 잠금이 풀릴 때까지 기다렸다가 수정 작업을 진행하게 됩니다.**


## SERIALIZABLE 격리 수준
1. 트랜잭션 1이 테이블에 여러 로우를 읽기
2. 트랜잭션 2가 같은 테이블에 여러 로우를 새로 insert
3. 트랜잭션 1이 같은 테이블을 다시 읽으면 자신이 처음 읽었을 때와 달리 새로 추가된 로우가 있음을 감지 (이것을 허상 읽기 라고함)

허상 읽기는 여러 로우가 연관된 점만 빼면 재현 불가한 읽기 문제와 비슷합니다.

### SERIALIZABLE 격리 수준
허상 읽기 문제를 해결하려면 최고 격리 수준인 SERIALIZABLE 으로 올려야합니다. 이렇게 설정하면 전체 테이블에 읽기 잠금을 걸기 때문에 실행 속도는 가장 느립니다. 실무에서 요건을 추종하는 가장 낮은 수준으로 격리 수준을 선택하는 게 좋습니다.