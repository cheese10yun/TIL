
# 의존성 주입의 이해
> 출처 [스프링5.0 마스터](http://www.kyobobook.co.kr/product/detailViewKor.laf?ejkGb=KOR&mallGb=KOR&barcode=9791161751825&orderClick=LAG&Kc=)을 보고 정리한 내용입니다.

의존성 주입을 적절하게 사용하면 코드를 테스트할 수 있게 만드는 방법을 알수 있다.

1. 데이터 서비스와 통신하는 비즈니스 서브스의 간단한 예를 작성하라. **비즈니스 서비스가 데이터 서비스의 인스턴스를 직접 생성하면 서로 밀접하게 결합된다. 단위 테스트는 어려울 것이다.**
2. 데이터 서비스를 생성하는 책임을 비즈니스 서비스 밖으로 이동시켜 코드를 느슨하게 결합하라.
3. 스프링 IoC 컨테이너를 가져와 빈을 인스턴스화 하고 서로 연결하라
4. 스프링이 제공하는 XML 및 자바 구성 옵션을 살펴보라
5. 스프링 단위 테스트 옵션을 살펴보라
6. 모킹을 사용해 실제 단위 테스트를 작성하라


## 스프링 IoC 컨테이너

IoC는 제어의 역전 이라고 부른다. 다른 많은 프로그램들과는 다르게 제어의 역전 패턴이 자바 웹 개발에서 특히 인기를 끄는 이유는 **프로그램의 생명주기에 대한 주도권이 웹 애플리케이션 컨테이너에 있기 때문이다.** 게임 엔진의 경우에는 초기화,  실행, 종료 같이 애플리케이션의 흐름 제어에 관한 부분을 직접 만든다. 그리고 실행 중인 메인 루프가 포함되어 프로그램이 종료되기 전까지 계속 실행 안에서 동작한다.

그러나 자바 기반의 웹 프로그램 기반 시에는 doGet, doPost 같은 메서드들의 파라미터와 함께 호출돤다. 이런 상황에서 파일 처리이나 데이터베이스 처리를 하는 클래스들의 인스턴스를 관리를 해야하고, 일괄적으로 인스턴스 관리를 하기 위해서 객체의 생성을 관리할 수 있는 도구의 필요성이 대두 되었다

**이 와 같은 의미로 디자인 패턴의 원칙 중 하나로 의존성 관계역전 DIP가 있다.** 의존관계 역전 원칙은 두 가지 내용을 담고 있습니다. 

1. **상위 모듈은 하위 모듈에 의존해서는 안되고, 인터페이스에 의존해야한다.**
2. **추상화는 세부 상황에 의존해서는 안된다. 세부 사항이 추상화에 의존해야한다**

단순하게 생각해서 인터페이스를 활용함으로써 결합도를 낮추자는 뜻입니다. **하지만 자바에서는 인터페이스를 사용하더라도 결국 인스턴스화하기 위해서는 반드시 객체 생성에 필요한 코드가 수반되므로 결합도를 완전히 분리해 낼 수 없습니다. 결국 프로그램이 온전히 동작하기 위해서는 인스턴스화할 수 있는 코드에 대한 의존성을 잦게됩니다. 이것을 대신 해결 해주는 것이 바로 의존성 주입(DI) 입니다.**

최근에는 제어의 역전 이라는 말 뜻이 너무 광범위하고 일반적이어서 제어의 역전보다는 의존성 주입을 많이 사용합니다.


### 빈과 와이어링 정의
* 빈을 생성해야하는 클래스에서 @Repository, @Component, @Service 어노테이션을 사용해 처리할 수 있다.
* @Component 어노태이션은 스프링 빈을 정의하는 가장 일반적인 방법이다. @Service 어노테이션은 비즈니스 서비스 구성 요소에 사용된다.

## IoC 구현 방법

스프링에서는 의존성 주입 하는 방법이 크게 3가지가 있다. 각 방법의 소개와 장단점을 소개 해보겠다.

## DL
* 저장소에 저장되어 있는 빈(Bean)에 접근하기 위하여 개발자들이 컨테이너에서 제공하는 API 를 이용하여 사용하고자 하는 빈(Bean) 을 Lookup 하는 것


## DI
* 각 계층 사이, 각 클래스 사이에 필요로하는 의존 관계를 컨테이너가 자동으로 연결해주는것
* 각 클래스의 사이의 의존 관계를 빈 설정 정보를 바탕으로 컨테이너가 자동으로 연결해주는 것

### @Autowired Injection

```java
@Service
@Transactional
public class AccountService {
    @Autowired
    private AccountRepository accountRepository;
}
```

* `@Autowired` 어노테이션을 사용해서 의존성 주입을 한다.
* **순환 참조 의존성을 방지 할 수 있다.**
* POJO 스럽지 않고 테스트 코드 작성시 의존성 주입을 변경해서 진행하기 힘들다

#### 동작 원리
@Autowried를 의존성에 사용하면, 애플리케이션 콘텍스트는 일치하는 의존성을 검색한다. 기본적으로 오토와이어되는 모든 의존성이 필요하다 

* 일치하는 항목이 1개 있다: 찾고 있는 의존성 주입
* 일치하는 항목이 2개 이상 발견됐다 : 오토와이어링 실패
* 일치하는 항목이 없다 : 오토와이어링 실패
  
둘 이상의 후보가 발견된 경우에는 두 가지 방법으로 해결 할 수 있다.
* 후보 중 하나만 사용하려면 @Primary 어노테이션을 사용하라
  * 후보가 둘 이상 있을 때 @Primary 어노테이션을 사용한 빈이 호출 된다.
* 오토와이어링을 더욱 강화하려면 @Qualifier를 사용하라
  * @Primary 반대로 내가 주입 받고싶은 객체에 @Autowired 어노테이션과 함께 @Qualifier("xxx")을 사용하면 한정자를 가진 xxx가 주입된다.
  * 빈으로 등록시킬 @Component 어노테이션과 함께 @Qualifier("xxx")를 함께 작성해야한다.


### Setter Injection
```java
@Service
@Transactional
public class AccountService {
    private AccountRepository accountRepository;

    @Autowired
    public void setAccountRepository(AccountRepository accountRepository) {
        this.accountRepository = accountRepository;
    }
}
```

* 인자가 없는 생성자나 인자가 없는 static factory 메서드가 bean을 인스턴스화 하기 위하여 호출된 bean 의 setter 메서드를 호출하여 실체화하는 방법
* 세터를 생성 후 의존성 삽입 방식이기에 구현시에 조금더 유연할 수 있다.
* 순환 참조 의존성을 발생할 수 있다.
* **immutable 객체가 아니기 때문에 실수할 수 있는 확률이 높다.**

### Constructor Injection

```java
@Service
@Transactional
public class AccountService {
    private final AccountRepository accountRepository;

    public AccountService(AccountRepository accountRepository) {
        this.accountRepository = accountRepository;
    }
}
```
* 생성자를 이용하여 클래스 사이의 의존 관계를 연결
* 생성자 파라미터를 지정함으로써 생성하고자하는 객체가 불필요 하는 것을 명확하게 알 수 있다.
* **Setter 메서드를 제공하지 않음으로 간단하게 필드를 불변 값으로 지정할 수 있다.**
* **가장 POJO 스럽다**
  * `final` 키워드로 해당 객체가 반드시 외부에서 주입 받는다는 것을 잘표시해준다
* 코드량이 많다
  * Lombok의 `@RequiredArgsConstructor`등을 활용하면 해결 가능