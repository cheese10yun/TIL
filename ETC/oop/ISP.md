# ISP


* 사용하지 않은 의존성을 가지고 있다면
	- 그 인터페이스가 변경되면 재컴파일/빌드/배포됨
	- 독립적인 개발/배포가 불가하다는 의미
* SRP와도 연관
	- 한 기능에 변경이 발생하면 다른 기능을 사용하는 클라이언트에도 영향을 미침
* 사용하는 기능만 제공하도록 인터페이스만 제공


```java
public class Switch {
	private Light light;

	public Switch(Light light){
		this.light = light;
	}

	public void activate() {
		light.turnOn();
	}
}
```

![](https://i.imgur.com/qciA9fH.png)

* 문제점
	- SWitch가 Light에 의존적임
	- Switch는 Light 뿐 아니라 Fan, Motor 등도 turnOn할  수 있다.
	- Switch는 Light에 대해서 알면 안된다.

* 해결책
	- Switch는 Light에 대한 의존성을 갖지 않는다.
	- Switch와 Sitchable이 같은 패키지 배포/단위에 있게 한다.
	- Interface(Switchable)는
		- 클라이언트(Sitch)에 속한다
		- 구현체(Light)와는 관련이 없다.
		- 그러므로 인터페이스의 이름은 클라이언트와 연관된 것이어야한다.

## Fat Class를 만나면
* Interface를 생성하여 Fat Class를 클라이언트로부터 isolate 시켜야함
* Fat Class에서 다수 Interface를 구현
* Interface는 구현체보다 클라이언트와 논리적으로 결합되므로 클라이언트가 호출하는 메소드만 Interface에 정의되있다는 것을 확신할 수있음 ISP준수
