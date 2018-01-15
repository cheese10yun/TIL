# Spring Batch 개념 정리



![](https://i.imgur.com/Z40TfOw.png)


## Job
하나의 배치 작업을 정의, 예를들어 "API" 서버의 사용 로그 테이블의 데이타를 로그 분석 시스템으로 옮기는 배치

## Job Instance
배치가 실행되면, 각각의 실행을 `Instance`라고 한다. 예를들어 `Batch Job`이 매주 한번씩 수행된다고 할때. 각각의 수형되는 `Batch Job`을 `Batch Instance`라고 한다.

## Job Execution
배치가 실행될때, 각 배치의 시행시, 실제 수행된 `Execution`을 정의한다. 예를들어 `Batch Job` 이 월요일 오전에 수행되얼을떄, 철번쨰 실패하고, 두번째 Retry에 의해 성공되었다면, 이 각각 다른 `Job Execution`이 되고, 같은 작업을 시도하였기 때문에, 같은 `Job Instance`가 된다.

## Job Parameter
매번 배치 작업이 수행될때 마다 전달되는 `Parameter`이다. 예를 들어 매주 수행되는 배치가 있을때, 시작 시간 데이타를 읽을 범위 등을 지정하여 `Batch Job Instance`를 생성한다면. 일엏게 넘어간 인자가 `Job Parameter`이다.


* [Spring Batch 개념 정 리Posted by 조대협 ](http://bcho.tistory.com/763) 글 보고 다시 정리한 내용


# Quartz
