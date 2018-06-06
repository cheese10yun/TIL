# Equals를 재정의할 때는 일반 규약을 따르라.

* Equals 메서드는 재정의 하기 쉬워 보이지만 실수할 여지가 많다. 그 결과는 끔찍하다(아래 설명).
* 이러한 문제를 피하는 가장 간단한 방법은 equals 메서드를 재정의하지 않는 것이다.

## equals 메서드를 재정의 하지 않아도 되는 경우
* 각각의 객체가 고유하다
    * 값 대신 활성 개체를 나라태는 Thread 같은 클래스가 이 조건에 부합한다. 이런 클래스는 equals 메서드를 그대로 사용해도 된다.
* 클래스에 **논리적 동일성** 검사 방법이 있건 없건 상관없다.
    * 대표적으로 Random 클래스
* 상위 클래스에서 정의한 equals 메소드가 그대로 하위 클래스에서 사용하기에도 적당하다
    * Set 클래스는 AbstractSet의 equals 메서드를 그대로 사용한다
* 클래스가 private 또는 package-private로 선언되었고, equals 메서드를 호출할 일이 없다.
    * 논란의 소지가 있음
    * 클래스가 공개되지 않을 경우 equals 메서드 호출시 에러를 나게 하는 것



## equals 메서드를 재정의하는 경우
* 객체 동일성이 아닌 논리적 동일성을 개념을 지원하는 클래스일 때
* 상위 클래스의 equals가 하위 클래스의 필요를 충족하지 못할 때
* Value Class가 대표적인 예이다.(데이터베이스에서 조회한 회원 동일한지 PK가 동일하다면 같다고 봐야하는 논리적 동일성)

## equals 메서드는 동치 관계 (equivalence relation)을 구현한다.

### 반사성(reflexive): null이 아닌 참조 x와 y가 있을때, x.equals(y) 는 true 반환한다.
* 모든 객체는 자기 자신과 같아야 한다는 뜻이다. 일부로 깨뜨리기도 어려운 요구사항이다.
* 이 요구사항을 어기면 컬랙션의 contains 메서드는 방금 추가한 객체가 없다고 할것이다
    * contains 메서드는 equals 메서드를 통해서 동작하니

### 대칭성(Symmentry): null이 아닌 참조 x가 있을 때, x.equals(y)는 y.equals(z)가 true 일때만 true를 리턴한다
* 두 객체가 서로 같은지 물으면 같은 답이 나와야한다.
* 쉽게 실수할 수 있다.
* 이 것도 마찬가지로 컬렉션의 contains 등 다양한 곳에서 문제가 생긴다.

### 추이성(transitive): null 아닌 참조 z,y,z가 있을 때, x.equals(y)가 true이고 y.equals(z)가 true 이면  x.equals(z)도 true 이다.
* 첫 번째 객체가 두 번째 객체와 같고, 두 번째 객체가 세 번째 객체아 같다면 첫 번쨰 객체와 세 번째 객체도 같아야 한다는 뜻이다.
* 쉽게 실수할 수 있다.

### 일관성(consistent): null 아닌 참조 x와 y가 있을 때, equals를 통해 비교되는 정보에 아무 변환가 없다면 x.equals(y) 호출결과는 호출 횟수에 상관없이 같아야한다.
* 일단 같다고 판정된 객체들은 추후 변경되지 않는 한 계속 같아야 한다는 것이다.
* 변경 가능한 객체들 간의 동치 관계는 시간에 따라 달라질 수 있지만 변경 불가능 객체 사이의 동치 관계는 달라질 수 없다.
* **신뢰성이 보당되지 않는 자원들을 비교하는 equals를 구현하는 것은 삼가하라**

### null 아닌 참조 x에대해서 x.equals(null)은 항상 false 이다
* 모든 객체는 null과 동치 관계에 있지 아니한다는 요구조건
* NullPointerException 예외 발생 가능성

## 정리
1. == 연산자를 사용하여 equals의 인자가 자기 자신인지 검사하라.
    * 선능 최적화를 위한것, 가장 앞부분에거 검사하는 것이 좋다.
2. instanceof 연산자를 사용하여 인자의 자료형이 정확한지 검사하라
    * 그렇지 않다면 false 리턴
    * 두 번째 검사로직에 있는 것이 바림직, 컬랙션 equals도 동일하게 구현
3. equals의 인자로 정호가한 자료형으로 변경하라
    * 그 앞에 instnaceof를 사용한 검사코드를 두엇음으로, 형변환은 반드시 성공할것이다.
4. * **중요** 필드 각각이 인자로 주어진 객체의 해당 필드와 일치하는지 검사한다
    * 논리적 동일성을 검사한다
    * 기본 타입들은 == 으로 비교한다. (dobule, float, int...)
    * 객체 타입은 Compare 메서드, equals 메서드들 통해서 검사한다.
5. equals 메서드 구현을 끝냈다면 대창성, 추이성, 일관성의 세 속성의 만족되는지 검토하라
    * 테스트 코드를 반드시 작성해서 검사하라

## 추가 
* equals를 구현할 때는 hashCode로 재정의하라
* 너무 머리쓰지마라
    * 디비에있는 값일 경우에는 PK만 검사하면 된다 이런 내용인듯
* equals 메서드의 인자 형을 Objext에서 다른 것으로 바꾸지마라


## example code
```java
@Override
public boolean equals(Object o) {
    if (this == o) return true; // 1.  == 연산자를 사용하여 equals의 인자가 자기 자신인지 검사하라.
    if(!(o instanceof MemberId)) return false; //2. instanceof 연산자를 사용하여 인자의 자료형이 정확한지 검사하라, null 체크 까지 함

    MemberId memberId = (MemberId) o;  // 3. equals의 인자로 정호가한 자료형으로 변경하라
    return Objects.equals(id, memberId.id); // 4. * **중요** 필드 각각이 인자로 주어진 객체의 해당 필드와 일치하는지 검사한다
}
```

