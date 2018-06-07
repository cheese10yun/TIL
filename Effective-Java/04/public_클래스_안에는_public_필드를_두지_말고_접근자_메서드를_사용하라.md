# 14. public 클래스 안에는 public 필드를 두지 말고 접근자 메서드를 사용하라

```java
//이런 저급한 클래스는 절대로 public 으로 선헌하지 말것
class Point {
    public dobule x;
    public double y;
}
```

* 이런 클래스는 데이터 필드를 직접 조작할 수 있어 캡슐화의 이점을 누릴수가 없다.
* API를 변경하지 안ㄴㅎ고서는 내부 표현을 변경할 수 없고, 불변식도 강제할 수없고, 필드를 사용하는 순간에 어떤 동작이 실행되도록 만들 수도 없다.
* 객체 지향 개념에 충실하가조하는 프로그래머에게 이런 크래스는 저주와도 같다
* **항상 접근 private로 선언하고 Getter, Setter 메서드를 통해야한다**

## 예외
* **package-private 클래스나 private 중첩 클래스(nested class)는 데이터 필드를 공개하더라도 잘못이라 할 수없다**
    * 클래스가 추상화하려는 내용을 제대로 기술하기만 하면말이다.



```java
public final class Time {
    ...
    ...
    // 변경 불가능 필드를 외부로 공개하는 public 클래스 - 정말 이래야하는 지 의문
    public final int hour;
    public final int minute;
}
```
* 비록 final 키워드가 있다곤 하지만 public 으로 굳이 공개가 필요할지 의문이다.
    * 내 개인적인 생각은 왠만하면 getter 메서드로 뺴는 것이 올바르다.

