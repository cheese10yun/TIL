## DIP
* 상위 레벨의 정책은 하위 레벌의 상세 합에 의존하면 안된다.

### OOP의 핵심

* IoC를 통해 상위 레벨의 모듈을 하위레벨의 모듈로 부터 보호하는 것
	- OCP를 통해 새로운 요구사항을 반영할 수 있음
* OO deign은 Dependency management

### Dependency Inversion

![](https://i.imgur.com/dLqcuKG.png)

* 절차
	- A와 B 사이에 polumorphic Interface를 삽입
	- A는 uses Interface
	- B는 implements the interface
	- 컴파일 의존성을 런타임 의존성으로 변경
* 중요 개념!

	- 컴파일 타임 의존성, 런타임 의존성이 동이 하게된것을 인터페이스를 추가 시킴으로 서로 분리
	- A가 B에 의존하지 않고 인터페이스에 의존 하게 변경한다.
	- A는 인터페이스 변경되지 않는 이상 B의 변경에는 자유롭다.
