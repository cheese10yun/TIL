> 해당 문서는 [https://docs.spring.io/spring-cloud-task/](https://docs.spring.io/spring-cloud-task/docs/current/reference/#getting-started)를 참조 했습니다.

# TASK TABLE Schema

![](https://docs.spring.io/spring-cloud-task/docs/current/reference/images/task_schema.png)

## TASK_EXECUTION

Task 실행정보를 저장

Column Name | Required | Type | Filed Length | Notes
------------|----------|------|--------------|------
TASK_EXECUTION_ID | TURE | BIGINT | X | App 시작시 TASK_SEQ 생성, Task 실행시 입력
START_TIME | FALSE | DATETIME | X | Task 실행 시작 시간
END_TIME | FALSE | DATETIME | X | Task 종료 시작 시간
TASK_NAME | FALSE | VARCHAR | 100 | spring.application.name을 따라감, 없는 경우 Application 지정
EXIT_CODE | FALSE | INTEGER | X | 0 = 실패, 1 = 성공
EXIT_MESSAGE | FALSE | VARCHAR | 2500 | 종료메시지 따로 지정하지 않은 경우 NULL
ERROR_MESSAGE | FALSE | VARCHAR | 2500 | Error Message 따로 지정하지 않으면 ErrorStack 메시지 출력
LAST_UPDATED | TRUE | DATETIME | X | Task startup 값 or 외부에서 실행시 값지정 해야함
EXTERNAL_EXECUTION_ID | FALSE | VARCHAR | 250 | Cloud Task를 통해 외부 작업 ID를 지정할 수있음, `pring.cloud.task.external-execution-id` 에서 설정 가능
PARENT_TASK_EXECUTION_ID | FALSE | BIGINT | X | Spring Cloud Task를 사용하면 각각에 대한 상위 작업 ID를 저장할 수 있습니다


## TASK_EXECUTION_PARAMS
Task 실행시 파라미터 정보 저장

Column Name | Required | Type | Field Length
------------|----------|------|-------------
TASK_EXECUTION_ID | TRUE | BIGINT | X
TASK_PARAM | FALSE | VARCHAR | 2500

## TASK_TASK_BATCH
Task 실행시 배치 실행에 연결 정보를 저장

Column Name | Required | Type | Field Length
------------|----------|------|-------------
TASK_EXECUTION_ID | TRUE | BIGINT | X
JOB_EXECUTION_ID | TRUE | BIGINT | X


## TASK_LOCL
Spring Cloud Task를 사용하면 주어진 작업 이름으로 한 번에 하나의 작업 만 실행할 수 있도록 설정할 수 있습니다. (이미 실행중인 잡을 살행 못하게 하는 설정인거 같다)

Column Name | Required | Type | Field Length | Notes
------------|----------|------|--------------|------
LOCK_KEY | TRUE | CHAR | 36 | Lcok의 UUID
REGION | TRUE | VARCHAR | 100 | 해당 필드로 lock group을 지정할 수 있음
CLIENT_ID | TRUE | CHAR | 36 | App Lock의 task execution id
CREATED_DATE | TRUE | DATETIME | X | 등록된 날짜