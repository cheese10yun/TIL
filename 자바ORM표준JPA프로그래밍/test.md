## 프록시와 컬렉션 래퍼

지연 로딩으로 설정하면 실제 엔티티 대신에 프록시 객체를 사용한다. 프록시 객체는 실제 자신이 사용될 때 까지 데이터베이스를 조회하지 않는다.

```java
Member member = em.find(Member.class, "member1");
List<Order> orders = member.getOrders();
System.out.println("orders = " + orders.getClass().getName());
// 결과: orders = org.hiberate.collection.interal.PersistenBag
```
**하이버네이트는 엔티티를 영속 상태로 만들 때 엔티티에 컬렉션이 있으면 컬렉션을 추적하고 관리할 목적으로 원본 컬렉션을 하이버네이트가 제공하는 내장 컬렉션으로 변경하는데 이것을 컬렉션 래퍼라 한다.** 

엔티티를 지연 로딩하면 프록시 객체를 사용해서 지연 로딩을 수행하지만 주문 내역 같은 컬렉션은 컬렉션 래퍼가 지연 로딩을 처리해준다. 컬렉샨 래퍼도 컬렉션에 대한 프록시 역활을 하므로 따로 구분하지 않고 프록시로 부른다.

```java
member.getOrders(); // 호출해도 컬렉션은 초기화되지 않는다.
member.getOrders().get(0); // 처ㅓ럼 컬렉션에 실제 데이터를 조회할 때 데이터베이스를 조회해서  초기화한다.
```