
## @Transcational 어노테이션

속성명             | 역할
----------------|-------------------------------------------------
isolation       | 격리 수준을 의미
propagation     | 전파 규칙을 정의
readOnly        | 트랜잭션을 읽기 전용으로 설정
rollbackFor     | 정의된 Exception에 대해 rollback 수행
no-rollback-for | rollbackFor와는 반대로 Exception이 발생해도 롤백을 정용하지 않은 설정
timeout         | 지정된 시간 내에 메서드 수행이 완료되지 않으면 롤백하는 설정


## @Transactional(readOnly = true)

JPA 구현체를 하이버네이트를 사용할 때 트랜잭션을 `readOnly`를 사용하면 다음 과같은 장점이 있다.

* Flush 모드를 `NEVER`으로 설정하게 되면 Isolation Level `Dity READ`으로 변경되서 성능상의 이점이 있다.

> **더티 읽기(Dirty Read)**
> 
> 어떤 트랜잭션이 커밋되기 전에 다른 트랜잭션에서 데이터를 읽는 현상이다. 예를들면 사용자 A가 값을 변경하고 아직 커밋하지 않아도 사용자 B가 변경한 후의 값을 읽는 것을 가리킨다.