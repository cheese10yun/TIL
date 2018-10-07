
## 목차
<!-- TOC -->

- [목차](#%EB%AA%A9%EC%B0%A8)
- [스프링 부트 배치의 장점](#%EC%8A%A4%ED%94%84%EB%A7%81-%EB%B6%80%ED%8A%B8-%EB%B0%B0%EC%B9%98%EC%9D%98-%EC%9E%A5%EC%A0%90)
- [스프링 부트 배치 주의사항](#%EC%8A%A4%ED%94%84%EB%A7%81-%EB%B6%80%ED%8A%B8-%EB%B0%B0%EC%B9%98-%EC%A3%BC%EC%9D%98%EC%82%AC%ED%95%AD)
- [스프링 부트 배치 이해하기](#%EC%8A%A4%ED%94%84%EB%A7%81-%EB%B6%80%ED%8A%B8-%EB%B0%B0%EC%B9%98-%EC%9D%B4%ED%95%B4%ED%95%98%EA%B8%B0)
    - [Job](#job)
    - [JobInstance](#jobinstance)
    - [JobExcution](#jobexcution)
    - [JobParameters](#jobparameters)
    - [Step](#step)
        - [StepExcution](#stepexcution)
    - [JobRepository](#jobrepository)
    - [JobLauncher](#joblauncher)
    - [ItemReader](#itemreader)
    - [ItemProcessor](#itemprocessor)
    - [ItemWriter](#itemwriter)
- [휴먼회원 배치 설계](#%ED%9C%B4%EB%A8%BC%ED%9A%8C%EC%9B%90-%EB%B0%B0%EC%B9%98-%EC%84%A4%EA%B3%84)
- [휴먼회원 배치 구현](#%ED%9C%B4%EB%A8%BC%ED%9A%8C%EC%9B%90-%EB%B0%B0%EC%B9%98-%EA%B5%AC%ED%98%84)
- [참고](#%EC%B0%B8%EA%B3%A0)

<!-- /TOC -->

스프링 배치는 벡엔드의 배치처리 기능을 구현하는 데 사용하는 프레임워크입니다. 스프링 부트 배치는 스프링 배치 설정 요소들을 간편화시켜 스프링 배치를 빠르게 설정하는 데 도움을 줍니다.

## 스프링 부트 배치의 장점
* 대용량 데어터 처리에 최적화되어 고성능을 발휘합니다.
* 효과적인 로깅, 통계 처리, 트랜잭션 관리 등 재사용 가능한 필수 기능을 지원합니다.
* 수동으로 처리하지 않도록 자동화되어 있습니다.
* 예외사항과 비정상 동작에 대한 방어 기능이 있습니다.
* 스프링 부트 배치는 반복적인 작업 프로세스를 이해하면 비니지스로직에 집중할 수 있습니다.

## 스프링 부트 배치 주의사항
스프링 부트 배치는 스프링 배치를 간편하게 사용 할 수 있게 래핑한 프로젝트입니다. 따라서 스프링 부트 배치와 스프링 배치에 모두에서 다음과 같은 주의사항을 염두해야 합니다.

* 가능하면 단순화해서 복잡한 구조와 로직을 피해야합니다.
* 데이터를 직접 사용하는 편이 빈번하게 일어나므로 데이터 무결성을 우지하는데 유효성 검사 등의 방어책이 있어야합니다.
* 배치 처리 시스템 I/O 사용을 최소화해야합니다. 잦은 I/O로 데이터베이스 컨넥션과 네트워크 비용이 커지면 성능에 영향을 줄 수 있기 때문입니다. 따라서 가능하면 한번에 데이터를 조회하여 메모리에 저장해두고 처리를 한 다음. 그결과를 한번에 데이터베이스에 저장하는것이 좋습니다.
* 일반적으로 같은 서비스에 사용되는 웹 API, 배치, 기타 프로젝트들을 서로 영향을 줍니다. 따라서 배치 처리가 진행되는 동안 다른 프로젝트 요소에 영향을 주는 경우가 없는지 주의를 기울여야합니다.
* 스프링 부트는 배치 스케줄러를 제공하지 않습니다. 따라서 배치 처리 기능만 제공하여 스케줄링 기능은 스프링에서 제공하는 쿼치 프레임워크 등을 이용해야합니다. **리눅스 crontab 명령은 가장 간단히 사용 할 수 있지만 이는 추천하지 않습니다.** crontab의 경우 각 서버마다 따로 스케줄리을 관리해야 하며 무엇보다 클러스터링 기능이 제공되지 않습니다. 반면에 쿼티 같은 스케줄링은ㄴ 프레임워크를 사용한다면 클러스터링뿐만 아니라 다양한 스케줄링 기능, 실행 이력 관리 등 여러 이점을 얻을 수 있습니다.

## 스프링 부트 배치 이해하기
배치의 일반적인 시나리오는 다음과 같은 3단계로 이루어집니다.

1. 읽기(read) : 데이터 저장소(일반적으로 데이터베이스)에서 특정 데이터 레코드를 읽습니다.
2. 처리(processing) : 원하는 방식으로 데이터 가공/처리 합니다.
3. 쓰기(write) : 수정된 데이터를 다시 저장소(데이터베이스)에 저장합니다.

배치 처리는 읽기 -> 처리 -> 쓰기 흐름을 갖습니다. 다음 그림은 스프링에서 이러한 배치 처리를 어떻게 구현 했는지 배치 처리와 관련된 객체의 관계를 보여줍니다.

<p align="center">
  <img src="/assets/batch-obejct-relrationship.png">
</p>

* Job과 Step은 1:M
* Step과 ItemReader, ItemProcessor, ItemWriter 1:1 
* Job이라는 하나의 큰 일감(Job)에 여러 단계(Step)을 두고, 각 단계를 배치의 기본 흐름대로 구성합니다.

### Job
* Job은 배치 처리 과정을 하나의 단위로 만들어 포현한 객체입니다. 또한 전체 배치 처리에 있어 항상 최상단 계층에 있습니다.
* 위에서 하나의 Job(일감) 안에는 여러 Step(단계)이 있다고 설명했던 바와 같이 **스프링 배치에서 Job 객체는 여러 Step 인스턴스를 포함하는 컨테이너 입니다**
* Job 객체를 만드는 빌더는 여러 개 있습니다. 여러 빌더를 통합합 처리하는 공장인 JobBuilderFactory로 원하는 Job을 쉽게 만들수 있습니다.

```java
public class JobBuilderFactory {
    private JobRepostiroy jobrepository;

    public JobBuilderFactory(JobRepository jobRepository){
        this.jobrepository = jobrepository;
    }

    public JobBuilder get(String name){
        JobBuilder builder = new JobBuilder(name).repository(jobrepository);
        return builder;
    }
}
```
* JobBuilderFactory는 JobBuilder를 생성할 수 있는 get() 메서드를 포함하고 있습니다. get()메서드는 새로운 JobBuilder를 생성해서 반환하는 것을 확인할 수있습니다.
* JobBuilderFactory에서 생성되는 모든 JobBulder가 레포지토리를 사용합니다.
* JobBuilderFactory는 JobBuilder를 생성하는 역할만 수행합니다. 이렇게 생성된 JobBuilder를 이용해서 Job을 생성해야 하는데, 그렇다면 JobBuilder의 역할은 무엇인지 JobBuilder의 메서드를 통해 기능을 알아보겠습니다.

```java
public SimpleJobBuilder start(Step step){
    //(1)
    // Step을 추가해서 가장 기본이되는 SimpleJobBuilder를 생성합니다.
    return new SimpleJobBuilder(tihs).start(step);
}

public JobFlowBuilder start(Flow flow){
    //(2)
    // Flow를 실행할 JobFlowBuilder를 생성합니다.
    return new JobFlowBuilder(tihs).start(flow);
}

public JobFlowBuilder flow(Step step){
    //(3)
    // Step을 실행할 FlowJobBuilder를 생성합니다.
    return new JobFlowBuilder(tihs).start(step);
}
```
* JobBuilder는 직접적으로 Job을 생성하는 것이 아니라 별도의 구체적 빌더를 생성하여 변환하여 경우에 따라 Job 생성 방법이 모두 다를 수 있는 점을 유연하게 처리할 수 있습니다.

### JobInstance
**JobInstance는 배치 처리에서 Job이 실행될 떄 하나의 Job 실행 단위입니다.** 만약 하루에 한 번 씩 배치의 Job이 실행된다면 어제와 오늘 실행 각각 Job을 JobInstance라고 부를 수 있습니다.

각각의 JobInstance는 하나의 JobException을 갖는 것은아닙니다. 오늘 Job이 실행 했는데 실패했다면 다음날 동일한 JobInstance를 가지고 또 실행합니다. Job 실행이 실패하면 JobInstance가 끝난것으로 간주하지 않기 때문입니다. 그렇다면 JobInstance는 어제 실패한 JobExcution과 오늘의 성공한 JobExcution 두 개를 가지게 됩니다. **즉 JobExcution 는 여러 개 가질 수 있습니다.**

### JobExcution
JobExcution은 JobIstance에 대한 한 번의 실행을 나타내는 객체입니다. 

만약 오늘 Job이 실패해 내일 다시 동일한 Job을 실행하면 오늘/내일의 실행 모두 같은 JobInstance를 사용합니다.

실제로 JobExcution 인터페이스를 보면 Job 실행에 대한 정보를 담고 있는 도메인 객체가 있습니다. JobExcution은 JobInstance, 배치 실행 상태, 시작 시간, 끝난 시간, 실패했을 때 메시지 등의 정보를 담고 있습니다. JobExcution 객체 안에 어떤 실행 정보를 포함 하고 있습니다.

### JobParameters
JobParameters는 Job이 실행될 때 필요한 파라미터들은 Map 타입으로 지정하는 객체 입니다.

JobParameters는 JobInstance를 구분하는 기준이 되기도 합니다.

JobParameters와 JobInstance는 1:1 관계입니다.

### Step
Step은 실직적인 배치 처리를 정희하고 제어 하는데 필요한 모든 정보가 있는 도메인 객체입니다. Job을 처리하는 실질적인 단위로 쓰입니다. 모든 Job에는 1개 이상의 Step이 있어야 합니다.

#### StepExcution
Job에 JobExcution Job실행 정보가 있다면 Step에는 StepExcution이라는 Step 실행 정보를 담는 객체가 있씁니다.

### JobRepository
JobRepository는 배치 처리 정보를 담고 있는 매커니즘입니다. 어떤 Job이 실행되었으면 몇 번 실행되었고 언제 끝났는지 등 배치 처리에 대한 메타데이터를 저장합니다.

예를들어 Job 하나가 실행되면 JobRepository에서는 배치 실행에 관련된 정보를 담고 있는 도메인 JobExcution을 생성합니다.

JobRepository는 Step의 실행 정보를 담고 있는 StepExcution도 저장소에 저장하여 전체 메타데이터를 저장/관리하는 역할을 수행합니다.

### JobLauncher
JobLauncher는 Job. JobParamerters와 함께 배치를 실행하는 인터페이스입니다. 

### ItemReader
ItemReader는 Step의 대상이 되는 배치 데이터를 읽어오는 인터페이스입니다. File, Xml Db등 여러 타입의 데이터를 읽어올 수 있습니다.

### ItemProcessor
ItemProcessor는 ItemReader로 읽어 온 배치 데이터를 변환하는 역할을 수행합니다. 이 것을 분리하는 이유는 다음과 같습니다.

* 비지니스 로직의 분리 : ItemWriter는 저장망 수행하고, ItemProcessor는 로직 처리만 수행해 역할을 명확하게 분리합니다.
* 읽어온 배치 데이터와 씌여질 데이터의 타입이 다를 경우에 대응할 수 있기 때문입니다.

### ItemWriter
ItemWriter는 배치 데이터를 저장합니다. 일반적으로 DB나 파일에 저장합니다.

ItemWriter도 ItemReader와 비슷한 방식을 구현합니다. 제네릭으로 원하는 타입을 받고 write() 메서드는 List를 사용해서 저장한 타입의 리스트를 매게변수로 받습니다.


## 휴먼회원 배치 설계
<p align="center">
  <img src="/assets/bach-process.png">
</p>

**가입한 회원 중 1년이 지나도록 상태 변화가 없는 회원을 휴면회원으로 전환하는 배치 처리**

* (1) DB에 저장된 데이터 중 1년간 업데이트되지 않은 사용자를 찾는 로직 ItemReader 구현합니다.
* (2) 대상 사용자 데이터의 상탯값을 휴면으로 전환하는 프로세스를 ItemProcessor에 구현합니다.
* (3) 상태값이 변환된 휴먼회원을 실제DB에 저장하는 ItemWriter를 구현합니다.

## 휴먼회원 배치 구현

배치처리 순서는 다음과 같습니다.

1. 휴면 회원 Job 설정
2. 휴먼회원 Step 설정
3. 휴면회원 Reader, Processor, Writer 설정


```java
@Configuration
public class InactiveUserJobConfig {
    ...
    @Bean
    public Job inactiveUserJob(JobBuilderFactory jobBuilderFactory, Step inactiveJobStep) { //(1)
        return jobBuilderFactory.get("inactiveUserJob")
                .preventRestart() //(2)
                .start(inactiveJobStep) //(3)
                .build();
    }
```

* (1) Job 생성을 직관적이고 편리하게 도와주는 빌더 JobBuilderFactory를 주입받습니다.
* (2) inactiveUserJob 이라는 JobBuilder를 생성하며 `preventRestart()` 설정을 통해 재실행을 막았습니다.
* (3) `start(inactiveJobStep)`은 파라미터에서 주입받은 휴먼회원 관련 Step인 inactiveJobStep을 제일 먼저 실행하도록 설정하는 부분입니다. 

기본적인 Job설정은 완료 했습니다. Step 설정을 진행하겠습니다.

```java
...
@Bean
public Step inactiveJobStep(StepBuilderFactory stepBuilderFactory) {
    return stepBuilderFactory.get("inactiveUserStep") //(1)
            .<User, User> chunk(10) //(2)
            .reader(inactiveUserReader()) //(3)
            .processor(inactiveUserProcessor())
            .writer(inactiveUserWriter())
            .build();
}
```
* (1) `stepBuilderFactory.get("inactiveUserStep")`로 inactiveUserStep 이라는 이름의 StepBuilder를 생성합니다.
* (2) 제네릭을 사용해서 `chunk()` 의 입력과 추력 타입을 User로 설정 했습니다. chunk의 인자값은 10으로 설정해서 **쓰기 시에 청크 단위로 writer() 메서드를 실행시킬 단위를 지정했습니다. 즉 커밋단위가 10개입니다.**
* (3) step의 reader, proccsor, writer를 각각 설정했습니다.

```java
@Bean
@StepScope //(1)
public QueueItemReader<User> inactiveUserReader() {
    //(2)
    List<User> oldUsers =
            userRepository.findByUpdatedDateBeforeAndStatusEquals(
                    LocalDateTime.now().minusYears(1),
                    UserStatus.ACTIVE);

    return new QueueItemReader<>(oldUsers); //(3)
}
```
* (1) 기본 빈 생성은 싱글턴이지만 @StepScope를 사용하면 해당 메서드는 Step의 주기에 따라 새로운 빈을 생성합니다. **즉, 각 Step의 실행마다 새로운 빈을 만들기 때문에 지연 생성이 가능합니다. 주의할 사항은 @StepScode는 기본 프록시 모드가 반환되는 클래스 타임을 참조하기 때문에 @StepScode를 사용하면 반드시 구현된 반환 타입을 명시해 변환해야합니다.** 해당 예제는 QueueItemReader<User>라고 명시했습니다.
* (2) `findByUpdatedDateBeforeAndStatusEquals()` 메서드를 통해서 휴먼 회원 리스트를 가져옵니다.
* (3) QueueItemReader 객체를 생성하고 불러온 휴먼회원 타깃 대상을 데이터 객체에 넣어 반환합니다.

```java
public class QueueItemReader<T> implements ItemReader<T> {
    private Queue<T> queue;

    public QueueItemReader(List<T> data) {
        this.queue = new LinkedList<>(data); //(1)
    }

    @Override
    public T read() throws Exception, UnexpectedInputException, ParseException, NonTransientResourceException {
        return queue.poll(); //(2)
    }
}
```
QueueItemReader는 큐를 사용해서 자장하는 ItemReader 구현체입니다. ItemReader의 기본 반환 타입은 단수형인데 그 에 따라 구현하면 User 객체 1개씩 DB에 select 요청 하므로 매우 비효율적인 방식이 될 수 있씹느다.

* (1) QueueItemReader를 사용해서 휴면회원으로 지정될 타깃 데이터를 한번에 불러와 큐에 담아 놓습니다.
* (2) reade() 메서드를 사용할 때 큐의 `poll()`메서드를 통해서 큐에서 데이터를 하나씩 반환합니다.

```java
public ItemProcessor<User, User> inactiveUserProcessor() {
    return user -> user.setInactive();
}
```
읽어온 타깃 데이터를 휴먼 회원으로 전환시키는 Processor입니다. reader에서 읽은 User를 휴면 상태로 전환화는 Processor 메서드를 추가하는 예입니다. 

```java
public ItemWriter<User> inactiveUserWriter() {
    return ((List<? extends User> users) -> userRepository.saveAll(users));
}
```
ItemWriter는 리스트 타입을 앞서 설정한 청크 단위로 받습니다. 청크 단위를 10으로 설정했기 때문에 users에게 휴면회원 10개가 주어지며 saveAll()메서드를 통해서 한번에 DB에 저장합니다.




## 참고
* [처음으로 배우는 스프링 부트 2](https://kyobobook.co.kr/product/detailViewKor.laf?mallGb=KOR&ejkGb=KOR&barcode=9791162241264&orderClick=JAj)를 정리한 글입니다.