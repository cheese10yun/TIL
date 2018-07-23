# 스타트 스프링 부트

## Spring Boot 장점

### 자동화된 라이브러리 관리
* 기존에도 메이븐, 그레이들을 이용해 라이브러리를 추가하는 잡업 등을 해 왔다면 스프링 부트에서는 이작업을 더욱 간단히 처리할 수 있습니다.

### Spring Boot Auto Configure(자동 설정)
* 스프링 부트에서 현재 프로젝트에 추가된 라이브러리를 이용해 자동으로 시행에 필요한 환경을 구성합니다.

### 적당한 라이브러리 자동화 결정과 XML 없는 환경 구축
* 스프링 부트를 이용하면 현재 버전에 맞는 관련 라이브러리들을 자동으로 결정함
* 라이브러리 버전이 높거나 낮아서 정상적으로 동작이 안되는 상황을 껵을 필요가 없게됨
* 이전 처럼 XML을 이용해서 라이브러리를 매번 설정하는 과정을 줄임

### 테스트환경과 내장 Tomcat
* 스프링 부트를 이용해서 생성하는 프로젝트는 기본적으로 Tomcat을 내장하고 있어 실행 역시 별도의 설정없이 main 메소드를 실행하는 방식으로 서버를 구동하기 때문에 빠르게 결과를 확인할 수 있습니다.


## 스프링 부트 내의 Bean 테스트하기
```java
@RestController
public class SampleController {

    @GetMapping("/hello")
    public String sayHello(){
        return "Hello World!";
    }
}
```
* @RestController 어노테이션을 이용해서 스프링의 빈으로 등록됨
* 기존 스프링에서는 어노테이션을 이용하더라도 `<component-scan>` 등과 같은 별도의 설정 작업이 필요 함
* @RestController를 사용시 HTML 등과 같은 별도의 뷰를 활요허지 않고 문자열을 리턴 Reset API 에 적함

## Lombok
* Getter/Setter, toString, 생성자를 자동으로 생성 해주는 플러그인
* 그 외에 다양한 기능이 있음, 반복적인 코드들을 자동으로 만들어 생산성을 높임

## Test

### Controller Test

```java
@RunWith(SpringRunner.class)
@WebMvcTest(SampleController.class)
public class SampleControllerTest {

    @Autowired
    MockMvc mock;

    @Test
    public void testHello() throws Exception{
        mock.perform(get("/hello")).andExpect(content().string("Hello World"));
    }
}
```

* MockMvc 객체의 경우 perform을 이용해 객체를 바우저에서 서버의 ULR을 호출하듯이 테스트를 진행할 수 있음
* 그 결과를 andExpect 를 이용해서 확인 가능
* @Test 어노테이션이 선언된 메소드를 시행하거나 클래스를 JUnit으로 실행하면 자동으로 스프링 부트가 시작되고 해당 테스트가 진행됨

## Spring Data JPA

* Java를 이용해서 데이터를 관리하는 기법으로 하나의 스펙으로 정리한 표쥰
* OMR은 객체지햐에서 말하는 객체와 데이터베이스에서 말하는 객체가 유사하다는 입장에서 시작됨
* ORM 자체는 객체지향과 관계햐ㅕㅇ 데이터베이스를 매핑시킨다는 추상화 개념
* JPA 그 자체로는 스펙에 불과하기 때문에 이를 실제로 구현하는 제품이나 프레임워크들의 존재가 필수적임

### JPA 장점

* 데이터베이스 관련 코드에 대해 유연함을 얻을 수 있습니다.
	- 데이터베이스를 변경하면 기존의 소스 코드에 치명적인 영향을 미침
	- 데이터베이스가 변경되면 이를 사용하는 SQL이 변경되고 DAO나 서비스, 화면까지 영향을 미침
	- 반면 JPA를 이용하면 테이블 생성 등과같은 작업으로 부터 변경되는 DB설계와 JAVA를 설계를 한번에 처리할 수있음
* 데이터베이스와 독릭적 관계
	- JPA는 특정 데이터베이스에 종속적이지 않기 때문에 개발자들은 데이터베이스 제품이 변경되거나 버전이 변경되나 같은 일에서 자유로워질수 있음

### JPA 단점
* 학습 곡선이 큽니다.
	- 기존 뎅터베이스 위주 학습에 비해서 새롭개 배워야하는 기능들이 많습니다.
* 근본적인 객체지향 설계사상이 반영되어야 합니다.
	- JPA는 어떻게 적합한 객체들의 관계를 작성할 것인가 가 가장 중요함
* 특정 데이터베이스의 강력함을 활용할수 없다는 문제
	- JPA를 이용하면 SQL 튜닝 등의 작업에 제약이 생길 수도 있습니다
	- JPA native SQL이라는 기능을 지원해주지만, 이 경우 데이터베이스의 독립적인 개바링 불가능하기 떄문에 JPA의 장점을 잃게되는 한계를 갖게됨

### JPA 개발 구조
* JPA를 이용하면JAVA로 작성된 클래스, 객체들과 데이터베이스상에 테이블, 레코드들을 자동으로 관리하게됨
* 개발자는 별도의 SQL을 작성할 피룡 없이 원하는 객체지향 구조를 설계하는 것만으로 모든 개발을 끝낼 수도 있습니다.

### 엔티티(Entity), 엔티티 매니저(EntityManager)

#### 엔티티
* 엔티티라는 용어는 데이터베이스상에서 데이터로 관리하는 대상을 의미
* 예를들어 상품, 회사, 직원 들과 같은 명사이면서 업무와 관련된 데이터를 엔티티로 규정
* 데티어베이스에서 엔티티를 위해서 일반적으로 테이블을 설계하고, 데이터를 추가함 이렇게 추가된 데이터는 인스턴스 혹은 레코드라는 용어로 호칭합니다.
* JPA에서 Java를 이용해서 이러한 엔티티들을 관리하기 때문에 엔티티 타입의 존재는 클래스가 됩니다
* JPA에서 하나의 엔티티 타입을 생성한다 라는 의미는 하나의 클래스를 작성한다는 의미
* JPA를 공부하면서 주의할 점은 엔티티라는 용어가 떄로는 클래스를 의미할 경우도 있지만 인스턴스를 의미하는 경우도 있다는 점입니다.

#### 엔티티 매니져
* 여러 엔티티 객체를 관리(Life Cycle)하는 역할
* 엔티티 매니저는 자신이 관리해야하는 엔티티 객체들을 영속 컨테스트 넣어두고 객체들의 생사를 관리하게 됨

#### 엔티티 메소드
* New(비영속)
	- Java 영역에 객체만 존재하고, 데이터베이스와 영동된 적이 없는 상태
	- 엔티티 매니저의 관리하에 있는 것이 아니기 때문에 순수한 Java 객체
* Managed(영속)
	- 데이터베이스에 저장되고, 메모리상에서 같은 상태로 존재하는 상태.
	- 객체는 영속 컨텐스트 내에 들어가게 되고, id(PK)값을 통해서 필요한 엔티티 객체를 꺼내 사용할 수 있음
* Removed(삭제)
	- 데이터베이스상에서 삭제된 상태.
	- 객체는 더이상 영속 컨텐스트에 존재하지 않음
* Detached(준영속)
	- 영속 컨텐스트에서 엔티티 객체를 꺼내서 사용하는 상태
	- 준영속 상태 객체 고유한 id(PK)를 가지고 있지만, 데이터베이스와 동기화가 이루지지 않은 상태


## JPA 설정
* application.properties 파일에서 설정 작업 진행
* `spring.jpa.hibernate.ddl-auto=?`
	- auto : 매번 테이블이 drop 되고 생성되기 떄문에 기존의 데이터를 확인할 수 없다
	- update : 매번 삭제 하지 않음


## @Query의 활용
* 리턴 값이 반드시 엔티티 타입이 아니라 필요한 몇 개의 칼럼 값들만 출출할 수 있다.
	- 엔티티 타입의 경우 타입의 모든 칼럼을 조회하지만, JPQL을 이용하면 필요한 몇 개의 칼럼들만 조회 할 수 있다.
* naviteQuery 속성을 지정해서 데이터베이스 사용하는 SQL을 그래로 사용할 수 있다.
	- SQL 자체 그대로 사용하고 싶을 때나 별도의 SQL에 대한 튜닝이 이루어지는 경우에 유용하게 사용할 수 있다
* Repository에 지정된 엔티티 타입 뿐 아니라 필요한 엔티티 타입을 다양하게 사용 할 수있다.
	- JPAQL의 경우 여러 엔티티 타입을 조화할 수 있기 때문에 한번에 여러 엔티티 타입을 조회하는 경우에 우용하게 사용할 수 있습니다.

## Querydsl 이용한 동적 SQL의 처리
* 쿼리를 처리하다 보면 다양한 상황에 맞게 쿼리르 생성하는 경우가 많음
* 대표적인 케이스가 다양한 검색 조건에 대해서 쿼리를 실행해야 하느 경우라고 할 수 있음
* 쿼리 메소드나 @Query를 이용하는 경우에 개발 속도는 좋지만 공적적인 쿼리만 생산합니다
* 따라서 동적인 상황에서 대한 처리를 위해서 Querydsl이라는 것 이용합니다.
* Querydsl 을 위해서 다음과 같으 과정을 거챠야함


## intellij Querydsl Setting

### pom.xml

```xml
<dependency>
    <groupId>com.querydsl</groupId>
    <artifactId>querydsl-apt</artifactId>
    <version>4.1.4</version>
    <scope>provided</scope>
</dependency>
<dependency>
    <groupId>com.querydsl</groupId>
    <artifactId>querydsl-jpa</artifactId>
    <version>4.1.4</version>
</dependency>
```
* pom.xml 라이브러리 추가

```
<build>
	<plugins>
		<plugin>
				<groupId>com.mysema.maven</groupId>
				<artifactId>apt-maven-plugin</artifactId>
				<version>1.1.3</version>
				<executions>
						<execution>
								<goals>
										<goal>process</goal>
								</goals>
								<configuration>
										<outputDirectory>target/generated-sources/java</outputDirectory>
										<processor>com.querydsl.apt.jpa.JPAAnnotationProcessor</processor>
								</configuration>
						</execution>
				</executions>
		</plugin>
	</plugins>
</build>
```
* 프러그인 추가

```
I use Maven (SpringBoot application) For this, solution

Right click project folder
Select Maven
Select Generate Sources And Update Folders
```
* 인텔리제이에서 플러그인 설정 ?

[link](https://stackoverflow.com/questions/5170620/unable-to-use-intellij-with-a-generated-sources-folder)


## Predicate 준비
* predicate는 쉽게 말해서 이 조건이 맞다고 판단하는 근거를 함수로 제공하는 것임
* 함수형 패러다임을 가진 언어에서 자주 사용됨 java8에서 추가됨



## 다양한 연관관계 처리

* 객체 간 여관관계 설정
* 단방향, 양반향 관계의 이해
* JPQL을 이용한 @Query 처리와 Fetch JSON

### 연관관계 처리와 순서 사전 설계

1. 필요한 각각의 클래스를 정의
2. 각 클래스의 연관관계에 대한 설정을 추가
  1. '일대다', '다대다' 등의 연관관계 설정
  2. 단방향, 양방향 설정
  3. 데이터베이스 상에 원하는 테이블이 만들어 지는지를 확인
  4. 테스트 코드를 통해 정상적으로 동작하는지를 확인

### JPA 연관관계 형태
* 일대일 : OneToOne
* 일대다 : OneToMany
* 다대일 : ManyToOne
* 다대다 : ManyToMany

### JPA 방향
* 단방향(Unidirectional) 참조 한쪽의 클래스만이 다른 클래스의 인스턴르를 참조하도록 설정
* 양방향(Bidirectional) 참조 양쪽 클래스 모두 다른 클래스의 인스턴스를 참조

### JPA - 단방향

* @JoinTable은 자동으로 생성되는 테이블 대신에 별도의 이름을 가진  테이블을 생성하고자할때 사용
* @JointColumn은 이미 존재하는 테이블에 칼럼을 추가할 때 사용

### 지연로딩
* JPA는 연관관계가 있는 엔티티를 조회할 떄 기본적으로 레이지 로딩을 이용한다
* 레이지는 게으른 이라는 의미로 정보가 필요하기 전까지는 최대한 테이브렝 접근하지 않는 방식


## @Jsonignore 어노테이션

* Spring MVC는 객체의 데이터를 자동으로 JSON 데이터로 처리해주는 jackson-databind를 이용해서 JSON을 변환을 함(spring boot 자동으로 다운)
* JSON 변환은 현재 객체를 JSON 으로 반환하는 것 외에도 객체가 잠조하고 있는 내부 객체 역시 JSON 으로 변환 된다
* 문제는 양뱡향의 경우에는 이러한 반환이 상호 호출이 되기 떄문에 무한이 반복되서 생성하는 문제가 생길수 있다느 점
* Lombok의 toString이 상호 호출되는 것과 동일한 문제
* 이를 해경하기 위해서 특정한 속성은 JSON 으로 변환되지 않도록 @JsonIgnore 어노테이션을 적용합니다.
