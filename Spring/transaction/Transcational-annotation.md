
## @Transcational 어노테이션

속성명             | 역할
----------------|-------------------------------------------------
isolation       | 격리 수준을 의미
propagation     | 전파 규칙을 정의
readOnly        | 트랜잭션을 읽기 전용으로 설정
rollbackFor     | 정의된 Exception에 대해 rollback 수행
no-rollback-for | rollbackFor와는 반대로 Exception이 발생해도 롤백을 정용하지 않은 설정
timeout         | 지정된 시간 내에 메서드 수행이 완료되지 않으면 롤백하는 설정