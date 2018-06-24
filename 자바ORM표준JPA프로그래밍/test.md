## 영속성 전이 : CASCADE
특정 엔티티를 영속 상태로 만들 때 연관된 엔티티도 함께 양속 상태로 만들고 싶으면 영속성 전이 기능을 사용 하면된다. JPA는 CASCADE 옵션으로 영속성 전이를 제공한다. 쉡게 말해서 영속성 전이를 사용하면 부모 엔티티를 저장할 때 자식 엔티티도 함께 저장할 수 있다.


### 영속성 전이: 저장

```java
@Entity
class Parent {
    @Id
    private Long id;

    @OneToMany(mappedBy = "parent", cascade = Cascade.PERSIST)
    private List<Child> children = new ArrayList<>();
}

@Entity
class Child {
    @Id
    private Long id;

    @ManyToOne
    private Parent parent;
}
```

부모를 영속활 때 연관된 자식들도 함께 영속화 하라고 cascade = Cascade.PERSIST 옵션을 설정 했다.

```java
// 저장 코드

private static void save(){
    Child child1 = new Child();
    Child child2 = new Child();

    Parent parent = new Parent();
    
    child1.setParent(parent); // 연관관계 추가
    child2.setParent(parent); // 연관관계 추가
    
    parent.getChildren().add(child1);
    parent.getChildren().add(child1);

    // 부모 저장, 연관된 자식들 저장
    em.persist(parent);
}
```

### 영속성 전이: 삭제
방금 지정한 부모와 자식 엔티티를 모두 제거하려면 다음 코드와 같이 각각의 엔티티를 하나씩 제거해야한다.

```java
Parent findParent = em.find(Parent.class, 1L);
Child findChild1 = em.find(Child.class, 1L);
Child findChild2 = em.find(Child.class, 2L);

em.remove(findParent);
em.remove(findChild1);
em.remove(findChild2);
```
영속성 전이 엔티티를 삭제 할 때도 사용할 수 있다. cascade = Cascade.REMOVE 설정하면 다음 코드처럼 부모 엔티티만 삭제하면 연관된 자식 엔티티도 함께 삭제 된다.

```java
Parent findParent = em.find(Parent.class, 1L);
em.remove(findParent);
```
코드를 실행하면 DELETE SQL을 3번 싱핼 하고 부모는 물론 연관된 저식도 모두 삭제 한다.삭제 순서는 외래 키 제약조건을 고려해서 자식을 먼저 삭제하고 부모를 삭제한다.

cascade = Cascade.REMOVE를 설정하지 않고 이 코드를 실행하면 부모 엔티티만 삭제된다. **하지만 데이터베이의 부모 로우를 삭제하는 순간 자식 테이블에 걸려 있는 외래 키 제약조건으로 인해 데이터베이스에서 외래키 무결성 예외가 발생한다.**

## 고아 객체
JPA는 부모 엔티티와 연관관계가 끊어진 자식 엔티티를 자동으로 삭제하는 기능을 제공하는데 이것을 고아 객체 제거라 한다. 이 긴능을 사여ㅛㅇ해서 **부모 엔티티의 컬렉션에서 자식 엔티티의 참조만 제거하면 자식 엔티티가 자동으로 삭제 된다.**


```java
@Entity
class Parent {
    @Id
    private Long id;

    @OneToMany(mappedBy = "parent", orphanRemoval = true)
    private List<Child> children = new ArrayList<>();
}
```

```java
Parent parent1 = em.find(Parent.calss, id);
parent1.getChildren().remopve(0); // 자식 엔티티를 컬렉션에서 제거

// 실행 결과 SQL은 다음과 같다
// DELETE FROM CHILD WHERE ID = `?
```
사용 코드를 보면 컬렉션에서 첫 번째 자식을 제거했다. orphanRemoval = true 옵션으로 인해 컬렉션에 엔티티를 제겅하면 데이터베이스의 데이터도 삭제제거 된다. 고아 객체 제거 기능은 영속성 컨텍스트를 플러시 할 때 적용 되므로 플러시 시점에서 DELETE SQL이 실행된다.


```java
parent1.getChildren().clear(); // 모든 자식 엔티티를 제거하려면 clear를 사용하면 된다.
```
고아 객체를 정리해보자. 고아 객체 제거는 참조자 제거된 엔티티는 다른곳에서 참조 하지 않는 고아 객체로 보고 삭제하는 기능이다. **따라서 이 기능은 참조한 곳이 하나 일때만 사용해야 한다**. 쉽게 이야기해서 특정 엔티티가 개인 소유하는 엔티티에만 이 기능을 적용 해야한다. 이런 이유로 orphanRemoval은 @OneToOne, @OneToMany에서만 사용할 수있다.

## 정리
* JPA 구현체들은 객체 그래프를 마음껏 탐색 할 수 있도록 지원하는데 이 때 프록시 기술을 사용한다.
* 객체를 조회할 때 연관된 객체를 즉시 로딩하는 방법을 즉시 로딩라하 하고, 연관된 객체를 지연해서 로딩하는 방법을 지연 로딩이라 한다.
* 객체를 저장하거나 삭제할 때 연관된 객체도 함께 저장하거나 삭제할 수 있는데 이것을 영속성 전이라 한다.
* 부모 엔티티와 연관관계가 끊어진 자식 엔티티를 자동으로 삭제하려면 고아 객체 제거 기능을 사용하면 된다.