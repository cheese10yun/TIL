## 목차
<!-- TOC -->

- [목차](#%EB%AA%A9%EC%B0%A8)
- [Spring Batch Table](#spring-batch-table)
- [BATCH_JOB_INSTANCE](#batchjobinstance)
- [BATCH_JOB_EXECUTION](#batchjobexecution)
- [참고](#%EC%B0%B8%EA%B3%A0)

<!-- /TOC -->
## Spring Batch Table

![meta-data-erd](/assets/meta-data-erd.png)

## BATCH_JOB_INSTANCE

```sql
CREATE TABLE `BATCH_JOB_INSTANCE` (
  `JOB_INSTANCE_ID` bigint(20) NOT NULL,
  `VERSION` bigint(20) DEFAULT NULL,
  `JOB_NAME` varchar(100) NOT NULL,
  `JOB_KEY` varchar(32) NOT NULL,
  PRIMARY KEY (`JOB_INSTANCE_ID`),
  UNIQUE KEY `JOB_INST_UN` (`JOB_NAME`,`JOB_KEY`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
```

* JOB_INSTANCE_ID
    * BATCH_JOB_INSTANCE 테이블의 PK
* JOB_NAME
    * 수행한 Batch Job Name

**BATCH_JOB_INSTANCE 테이블은 Job Parameter에 따라 생성됩니다.**

Job Parameter는 Spring Batch가 실행될때 외부에서 받을 수 있는 파라미터 입니다. 

**같은 Batch Job 이라도 Job Parameter가 다르면 다른 BATCH_JOB_INSTANCE 에 기록됩니다.**

## BATCH_JOB_EXECUTION
```sql
CREATE TABLE `BATCH_STEP_EXECUTION` (
  `STEP_EXECUTION_ID` bigint(20) NOT NULL,
  `VERSION` bigint(20) NOT NULL,
  `STEP_NAME` varchar(100) NOT NULL,
  `JOB_EXECUTION_ID` bigint(20) NOT NULL,
  `START_TIME` datetime NOT NULL,
  `END_TIME` datetime DEFAULT NULL,
  `STATUS` varchar(10) DEFAULT NULL,
  `COMMIT_COUNT` bigint(20) DEFAULT NULL,
  `READ_COUNT` bigint(20) DEFAULT NULL,
  `FILTER_COUNT` bigint(20) DEFAULT NULL,
  `WRITE_COUNT` bigint(20) DEFAULT NULL,
  `READ_SKIP_COUNT` bigint(20) DEFAULT NULL,
  `WRITE_SKIP_COUNT` bigint(20) DEFAULT NULL,
  `PROCESS_SKIP_COUNT` bigint(20) DEFAULT NULL,
  `ROLLBACK_COUNT` bigint(20) DEFAULT NULL,
  `EXIT_CODE` varchar(2500) DEFAULT NULL,
  `EXIT_MESSAGE` varchar(2500) DEFAULT NULL,
  `LAST_UPDATED` datetime DEFAULT NULL,
  PRIMARY KEY (`STEP_EXECUTION_ID`),
  KEY `JOB_EXEC_STEP_FK` (`JOB_EXECUTION_ID`),
  CONSTRAINT `JOB_EXEC_STEP_FK` FOREIGN KEY (`JOB_EXECUTION_ID`) REFERENCES `BATCH_JOB_EXECUTION` (`JOB_EXECUTION_ID`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
```

* `JOB_EXECUTION_ID` 칼럼은 `BATCH_JOB_INSTANCE` 테이블의 PK를 참조 하고 있습니다.
* `BATCH_STEP_EXECUTION` 와 `BATCH_JOB_INSTANCE`는 부모 자식관계입니다.
* BATCH_STEP_EXECUTION는 자신의 부모 BATCH_JOB_INSTANCE 성공/실패 내역을 모두 갖고 있습니다.
* 
![job-job-instance-job-execution](/assets/job-job-instance-job-execution.png)

* `Job`: 특정 잡, 2달이상 로그인안한 유저 휴먼 회원 처리 등
* `Job Instance`: Job Parameter를 실행한 Job(Job Parameter 단위로 생성)
* `Job Execution`: Job Parameter로 실행한 Job의 실행, 1번 째 시도 혹은 그 다음 등


## 참고
* [기억보단 기록을 - spring-batch-in-action](https://github.com/jojoldu/spring-batch-in-action/blob/master/3_%EB%A9%94%ED%83%80%ED%85%8C%EC%9D%B4%EB%B8%94%EC%97%BF%EB%B3%B4%EA%B8%B0.md)