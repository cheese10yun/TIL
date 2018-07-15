<!-- TOC -->

- [1장 인터페이스](#1%EC%9E%A5-%EC%9D%B8%ED%84%B0%ED%8E%98%EC%9D%B4%EC%8A%A4)
    - [리스트가 두 종류인 이유](#%EB%A6%AC%EC%8A%A4%ED%8A%B8%EA%B0%80-%EB%91%90-%EC%A2%85%EB%A5%98%EC%9D%B8-%EC%9D%B4%EC%9C%A0)
    - [자바 inferface](#%EC%9E%90%EB%B0%94-inferface)
    - [List inferface](#list-inferface)

<!-- /TOC -->

# 1장 인터페이스

## 리스트가 두 종류인 이유

자바는 왜 List 인터페이스에 두 가지 구현을 제공할까? 둘 중에 어느것을 선택해야할까? 

처음 몇 가지 예제에서 Arralist, LinkedList와 유사한 클래스를 구현합니다. 어떤 동작에서는 ArrayList가 빠르거나 저장공간을 적세 사용하고 다른 사황에서는 LinkedList가 빠르거나 메모리 사용량이 적습니다. 어느 것이 더 좋을지는 수행하는 동작에 달렸습니다.

## 자바 inferface
자바 인터페이스는 메서드 집합을 의미합니다. 자바 Comparable inferface의 소스는 다음과 같습니다.

```java
public interace Comparable<T> {
    public int compareTo(T o);
}
```
이 인터페이스는 타입 파라미터 T를 사용해서 Comparable 이라는 제네릭 타입을 정의합니다.. 이 인터페이스를 구현하려면 클래스는 다음과 같아야합니다.
* T 타입을 명시해야합니다.
* T 타입의 객체를 인자로 받고 int를 반환하는 compareTo 메서드를 제공해야합니다.

## List inferface
필요한 경우가 아니라면 LinkedList나 ArrayList 같은 구현 클래스를 사용하지 않고 가능한 List 인터페이스를 사용한다는 점입니다. 예를 들어 인스턴스 변수는 List 인터페이스로 선언하고 getList 메서드도 List 형을 반환하지만 구체적인 클래스를 언급하지 않습니다.

마음을 바꿔 ArrayList 클래스를 사용하고자 하다면 생성자만 바꾸면 되고 그 외에는 그대로 두면 됩니다.

이러한 스타일을 인터페이스 기반 프로그래밍 (inerface-based programming) 또는 간한하게 인터페이스 프로그래밍이라고 합니다.

라이브러리는 사용할 때 코드는 오직 List와 같은 인터페이스만 의존하고 ArrayList 클래스와 같은 특정 구현에 의존해서는 안 됩니다. 이러한 방식으로 하면 나중에 구현이 변경되더라도 인터페이스를 사용하는 코드는 그대로 사용할 수 있습니다.

