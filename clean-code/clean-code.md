# Clean-Code
* [Git-Hub link](https://github.com/msbaek/clean-coders-2013)

## 1. Why Clean Code
* 소프트웨어는 한번 작성되면 최소 10 이상 읽힌다. 그래서 대충 돌아가게 작성하면 안되고 읽기 편하도록 작성해야된다.
* 기계가 이해할 수있는 코드는 어느 바보도 작성할 수 있다. 하지만 인간이 이해할 수있는 코드는 잘 훈려된 소프트웨어 엔지니어만 작성 할 수있다.


## 2. Why OOP
* 데이터와 그 데잍처를 조작하는 코드의 외부에 영향을 안미침
* 외부에 노출된 인터페이스만 변경되지 않는다면 프로시저를 실행하는데 필요한 칸큼의 데이터만 가짐


## 3. Object / Role / Responsibility
* 객체/클래스의 이름
	- 무엇으로 정의햐야 함 -> RequestPaster
	- 어떻게로 정의하지말고 -> JSONRequestPaser
* 역할은 관련된 책임의 집합
* 객첸ㄴ 역할을 가짐

## 4. 객체지향 설계 과정

1. 기능을 제공할 객체 후보 선별
	- 내부에서 필요한 데이터 선별
	- 클래스 다이어그램
	- 정적 설계
2. 객체 간 메세지 흐름 연결
	- 커뮤니케이션 다이어그램
	- 시퀀시 다이어그램
	- 동적 설계
3. 1, 2를 반복

## 5. Encapsulation

### 절차지향 StopWatch
```java
public class ProcedualStopWatch {
	public long startTime;
	public long stopTime;

	public long getElapsedTime(){
		return stopTime - startTime;
	}
}

public class ProcedualStopWatchTest {

	@Test
	public void
	should_return_elapsed_milli_seconds(){
		ProcedualStopWatch stopWatch = new ProcedualStopWatch();

		stopWatch.startTime = System.currentTimeMillis();

		doSomeThing();

		stopWatch.stopTime = System.currentTimeMillis();

		long elaspedTime = stopWatch.getElapsedTime();

	}
}
```
* 스톱워치를 밀리세컨드 타입으로 리턴하는 기능
* 나노세컨드 타입으로 기능이 추가 될 경우

```java
public class ProcedualStopWatch {
	public long startTime;
	public long stopTime;

	//추가
	public long startNanoTime;
	public long stopNanoTime;
	//추가

	public long getElapsedTime(){
		return stopTime - startTime;
	}

	//추가
	public long getNanoElapsedTime(){
		return stopTime - startTime;
	}
	//추가
}
```
* 클라이언트 부분에서도 변경이 심해짐

### 객체지향

```java
public class ProcedualStopWatch {
	private long startTime;
	private long stopTime;

	public void start(){
		startTime = System.nanoTime();
	}

	public void stop(){
		stopTime = System.nanoTime();
	}

	public Time getElapsedTime(){
		return new Time(stopTime - startTime);
	}
}

public class Time {
	private long nano;

	public Time(long nano){
		this.nano = nano;
	}

	public long getMilliTime(){
		return (long) (nano / pow(10, 6));
	}
}
```
* 절차비향에 비해 확실히 작은 변경의 영향
* 구현 변경의 유연함 클라이언트에 영향을 안미침

### Tell, Don't Ask
* 데이터를 요청해서 변경하고 저장하려 하지마라
* 무슨 기능을 실행 하라
* 데이터를 잘 알고 있는 객체에게 기능을 수행하라고 해라
* `Encapsulation`이 유지되어 변경에 영향을 안 받게 됨
	- `if(member.getExpireDate.getTime()) < System.currentTimeMillis){...}`를 사용하지말고
	- `if(member.isExpired()){...}`를 사용하라
