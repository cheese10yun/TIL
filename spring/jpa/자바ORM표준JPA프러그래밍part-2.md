## 10 객체지향 쿼리 언어
* JPA는 복잡한 검색 조건을 사용해서 엔티티 객체를 조회 할 수있는 다양한 쿼리 기술을 지원한다.
	- JPQL, Criteria, QueryDSL 등이 있다

### 객체지향 쿼리 소개
* EntityManager.find() 메소드를 사용하면 식별자로 엔티티 하나를 조회할 수있다. 이렇게 조회한 엔티티에 객체 그래프 탐색을 사용하면 연관된 엔티티들을 찾을 수 있다.
* 식별자로 검색 EntityManager.find();
* 개체 그래프 탐색 a.getB().getC();

* 이 기능만으로 애플리케이션 개발하기는 어렵다. 예를들어 나이가 30살 이상인 회원을 모두 검색하고 싶다면 좀더 현실적이고 복잡한 검색 방법이 필요하다
* 그렇다고 모든 회원 엔티티를 메모리에 올려두고 애플리케이션에서 30살 이상 인 회원을 검색하는 것은 현실성이 없다. 결국 데이터는 데이터베이스에있으므로 SQL로 필요한 내용을 최대한 거러서 조회햐야 한다
* 하지만 ORM을 사용하면 테이블이 아닌 엔티티 객체를 대상으로 하는 방법이 필요하다
* JPQL은 이런 문제를 해결하기 위해서 만들어졌는데 다음과 같은 특징이 있다.
	- 테이블이 아닌 객체를 대상으로 검색하는 객체지향 쿼리다.
	- SQL은 추상화해서 특정 데이터베이스 SQL에 의존하지 않는다
* SQL이 데이터베이스 테이블을 대상으로 하는 데이터 중심 쿼리라면 JPQL은 엔티티 객체를 대상으로 하는 객체지향 쿼리다.
* JPQL을 사용하면 이 JPQL을 분석한 다음 적절한 SQL을 만들어 데이터베이스를 조회한다. 그리고 조회한 결과로 엔티팇 객체를 생성해서 반환한다
* JPQL은 한마디로 정의하면 객체지향 SQL이다. 처음 보면 SQL로 오해할 정도로 문법이 비슷하다
* JPA는 JPQL뿐만 아니라 다양한 검색 방법을 제공한다. 다음은 JPA가 공식지원하는 기능이다.
	- JPQL
	- Criteria 쿼리 : JPQL을 편하게 작성하도록 조와주는 API, 빌더 클래스 모음
	- 네이티브 SQL : JPA에서 JPQL 대신 직접 SQL을 사용할 수있다
* 다음은 JPA가 공식 지원하는 기능은 아니지만 알아둘 가치가 있다.
	- QueryDSL : Criteria 쿼리처럼 JPQL을 편리하게 작성하도록 도와주는 빌더 클래스 모움, 비표준 오픈소스 프레임워크이다.
	- JDBC 직접 사용, MyBatis같은 SQL 매퍼 프레임워크사용

### JPQL 소개
* JPQL은 엔티티 객체를 조회하는 객체지향 쿼리다.
* JPQL은 SQL을 추상화해서 특정 데이터베이스에 의존하지 않는다.
* 데이터베이스 방언만 변경하면 JPQL을 수정하지 않아도 자연스럽게 데이터베이스를 변경할 수 있다.
* JPQL은 SQL보다 간결하다

```java
String jpql = "select m from Member as m where m.username = 'kim'";
List<Member> resultList = em.creeatedQuery(jpql, Member.class).getResultList();
```

* 회원이르이 kim인 엔티티를 조회한다. JPQL에서 Member는 엔티티 이름이다 그리고 u.sername은 테이블 칼럼명이 아니라 엔티티 객체의 필드 명이다.

### Criteria 쿼리소개
* Criteria는 JPQL을 생성하는 빌더 클래스다. Criteria의 장점은 문자가 아닌 `query.select(m).where(..)` 처럼 프로그래밍 코드로 JPQL을 작성할 수있다는 좀이다.
* 이러한 점은 문자열에 오타 같은 실수를 런타임 에러시에 잡기 힘든 부분을 보안
	- IDE에서 지원 받으면 어느정도 해결 가능할거같음
* 컴파일 시점에 오류를 발견할 수있다.
* IDE를 사용하면 코드 자동완성을 지원한다
* 동적 쿼리를 작성하기 편하다

### QueryDSL 소개
* QueryDSL도 Criteria 처럼 빌더 역할을 한다. QueryDSL의 장점은 코드 기반이며 단순하고 사용하기 쉽다. 그리고 작성한 코드도 JPQL 과 비슷해서 가독성이 좋다
* QueryDSL과 Criteria 비교하면 Criteria는 너무 복잡하다

```java
JPAQuery query = new JPAQuery(em);
QMember member = Qmember.member;

List<Memeber> members = query.from(member).where(member.username.eq("kim")).list(member);
```
* `QMember`는 Member 엔티티 클래스를 기반으로 생성한 QueryDSL 쿼리 전용 클래스다.

### 네이티브 SQL 소개
* JPA는 SQL을 직접사용할 수있는 기능을 지원흔데 이것을 네이티브 SQL이라 한다.
* JPQL을 사용해도 가끔은 특정 데이터베이스에 의존하는 기능을 사용해야할때 사용한다.

### JPQL
* 어떤 방법을 사용하든 JPQL에서 모든 것이 시작된다
* 이론은 소개 절에서 이미 설명했으니 JPQL의 사용 바업 위주로 설명하겠다
* JPQL은 객체지향 쿼리 언어다. 따라서 테이블을 대상으로 쿼리하는 것이 아니라 엔티ㅣㅌ 객체를 대상으로 쿼리한다.
* JPQL은 SQL을 추상화해서 특정 데이터베이스 SQL에 의존하지 않는다.
* JPQL은 결국 SQL로 변환된다.

### 기본 문법과 쿼리 API
* JPQL도 SQL과 비슷하게 select, update, delete 문을 사용할 수있다.
* 참고로 에니팉 저장할 떄는 EntityManager.persist() 메소드를 사용하면 되므로 insert 문은 없다.
*
```SQL
select_문 :: =
	select_절
	from_절
	[where_절]
	[group by_절]
	[having_절]
	[orderBy_절]

update_문 :: = update_절 [where_절]
delete_문 :: = delete_절 [where_절]
```
### SELECT 문

```sql
select m from Member as m where m.username = 'Hello'
```
* 대소문자 구분
	- 엔티티와 속성은 대소문자를 구분한다. 예를 들어 Member, username은 대소문자를 구분한다. 반면 select, from as 같은 JPQL 키워드는 갯문자를 구분하지 않는다.
* 엔티티 이름
	- JPQL에서 사용하는 Member는 클래스 명이 아니라 엔티티 명이다. 엔티티 명은 `@Entity`로 지정할 수있다. 엔티티 명을 지정하지 않으면 클래스 명을 기본값으로사용한다
* 별칭은 필수
	- Member as m 을 보면 member에 m이라는 별을을 주었다. JPQL은 별칭을 필수로 사용해야한다.

### TypeQuery, Query
* 작성한 JPQL을 실행하려면 쿼리 객체를 만들어야 한다. 쿼리 객체는 TypeQuery와 Query가 있는데 반환할 타입을 명확하게 지정할 수 있으면 TypeQuery를 객체를 사용하고 반환 타입을 명확할게 지정할 수 없으면 Query 객체를 사용하면된다.

### TypeQuery 사용
```java
TypedQuery<Member> query = em.createQuery("select m from Member m", Member.class)

List<Member> resultList = query.getResultList();
for(Member meber : resultList){
	System.out.println("member =" + member);
}
```
* ` em.createQuery()`의 두 번째 파라미터에 반환할 타입을 지정하면 TypeQuery를 변환하고 지정하지 않으면 query를 반환한다. 조회 대상이 Member 엔티티이므로 조회 대상 타입이 명확하다. 이럴 때 `TypedQuery`를 사영한다.


### Query 사용

```jqva
Query query = em.createQuery("select m.usrname, m,age from Member m");
List resultLsit = query.getResultList();
```
* 조회 대상이 stirng 타입인 회원 이름과 integer 타입인 나이이므로 조회 대상 타입이 명호가하지 않다. 이 처럼 select 절에서 여러 엔티티나 칼럼을 선태갛ㄹ 때 반환 타입이 명확하지 않으므로 query객체를 사용해야한다.

### 결과조회
* query.getResultLsit() : 결과를 예제로 변환한다 만약 결과가 없다면 빈 컬랙션을 반환한다.
* query.getSingleResult() : 결과가 정확히 하나일 때 사용한다.
	- 결과가 없으면 예외가 발생한다.
	- 결과과 1개 보다 많으면 예외가 발생한다.
	-
### 파라미터 바인딩

* JDBC는 위치 기준 파라미터 바인딩만 지원하지만 JPQL은 이름 기준 파라미터 바인딩도 지원한다

* 이름 기준 파라미터
	- 이름 기준 파라미터는 파라미터 이름으로 구분하는 방법이다. 이름 기준 파라미터는 앞에 : 를 사용한다.

### 프로젝션
* SELECT 절에 조회할 대상을 지정하는 것을 프로젝션 이라 하고 `[select {프로젝션 대상} from]` 으로 대상을 선택한다.
* 프로젝션 대상은 엔티티, 임비디드 타입, 스칼라 타입이 있다.
* 
