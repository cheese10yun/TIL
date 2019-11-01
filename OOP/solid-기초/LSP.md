# LSP

* [강의 자료 정리](https://www.youtube.com/watch?v=OfVwuWJSHOY)


```JAVA
public class LSP {
    static  P p = new P();

    static class T {
        public void  doSomething() {
            System.out.println("T#doSometing called");
        }
    }

    static class S extends T {
        public void doSomething(){ // 5. S는 T의 서브타입이다.
            System.out.println("S#doSometing called");
        }
    }

    static class P {
        public void doSomething(T p){ // 3. T 타입을 사용하는 모든 프로그램 p에서
            p.doSomething();
        }
    }

    public static void main(String[] args){
        T p = new T(); // 1. T 타입의 객체 p
        S c = new S(); // 2. S 타입의 객체 c

        LSP.p.doSomething(p); // 4. c가 p를 대체해도 P에는 어떤 변경도 없다면
        LSP.p.doSomething(c);
    }
}
```

```
1. T 타입의 객체 p
2. S 타입의 객체 c
3. T 타입을 사용하는 모든 프로그램 p에서
4. c가 p를 대체해도 P에는 어떤 변경도 없다면
5. S는 T의 서브타입이다.
```


* `public void doSomething(T p){...}` **에서 T 타입인지 S 타입인지 알 필요 없이 돌아가야 한다.**
* `InstanceOf`를 사용하지 않아야 한다는 말 타입에 대한 의존성을 제거 해야한다.(타입 의존성은 지독한 의존성이다.)
* **서브 타입의 클래스가 슈퍼 타입의 클래스를 대체 하여도 슈퍼 클래스에 어떤 변경도 없어야 LSP 준수**

## OCP vs LSP
* OCP
  - abstraction, polymorphism(inheritance)를 이용해서 구현
* LSP
  - OCP를 받쳐주는 polymorphism에 관한 원칙을 제공
  - LSP가 위반되면 OCP도 위반됨
  - LSP를 위반하면 subtype이 추가될때 마다 클라이언트들이 수정되어야함
  - InstanceOf/downcasting을 사용하는 것이 전형적인 LSP위반의 징조

## 나의 정리
* 서브 타입이나 슈퍼 타입에서 `InstanceOf/downcasting`를 사용해서 정확한 타입이 무엇인지 알아야 되는 일이 생기면 LSP 위반
* 서브 클래스에서 슈퍼 클래스를 사용하는 것은 좋지 않은 거같다.
* 서브 타입은 언제나 자신의 슈퍼타입 으로 교체 할 수 있어야 한다.
* 클래스 인 경우 하위 클래스 라면 상위 클래스의 한 종류여야 한다.
* 인터페이스 인 경우 구현 클래스는 인터페이스를 지켜야한다.
* 서브 클래스가 수퍼 클래스의 책임을 무시하거나 재정의 하지 않고 오직 확장만 수행 해야한다



## 리스코프 치환 원칙 : 계약
* 리스코프 치환 원칙은 계약에 관한 것이다.
* 계약 위반 사례
  * 명시된 명세에서 벗어난 값을 리턴한다.
  * 명시된 명세에서 벗어난 Exception을 발생한다.
  * 명시된 명세에서 벗어난 기능을 추생한다.


## 리스코프 치환 원칙 : 확장

* 리스코프 치환 원칙은 또한 확장에 대한 것이다.
* 리스코프 치환 원칙을 어기면 **개방 폐쇄 원칙을 어길 가능성이 높아진다.**


```java
public class Coupon {
    public int calculateDiscountAmount(Item item) {
        return item.getPrice() * discountRate;
    }
}
```

* Coupon 클래스에 calculateDiscountAmount() 메서는 item 클래스의 getPrice() 할인된 값을 구하고 있음
* 특정 Item은 무조건 할인 해주지 않은 정책이 추가되면, 이를 위해 Item 클래스를 상속받는 SpecialItem 클래스를 추가했다고 했을 경우
* Coupon 클래스의 calculateDiscountAmount 메서드는 item 객체의 실제 타입의 SpecialItem인 경우 할인 액수를 0으로 처리 해야함


```java
public class Coupon {
    public int calculateDiscountAmount(Item item) {
        if(item instanceof SpecialItem) return 0; // LSP 위반 발생

        return item.getPrice() * discountRate;
    }
}
```

* instanceof 코드는 대표적인 LSP 위반 사례이다.
* **Item 타입을 사용하는 코드는 SpecialItem 타입이 존재하는지 알 필요 없이 오직 Item 타입에만 사용해야 한다.**
* 위 코드는 instanceof 연산자를 통해서 SpecialItem 타입인지의 여부를 확인하고 있다. **즉 하위 타입인 SpecialItem이 상위 타입인 Item을 완벽하게 대체하지 못하는 상황이 발생하고 있다.**
* instanceof 연산자를 사용한다는 것은 상위 타입 Item으로만 사용해서 프로그래밍할 수 없다는 것을 뜻한다. **이는 SpecialItem과 같은 새로운 종류의 하위 타입이 생길 때마다 상위 타입인지를 사용하는 코드를 수정해야 한다. 이것은 OCP 원칙을 지킬수 없게된다** 
* **LSP를 어기게 된 이유는 Item에 대한 추상화가 덜 되었기 때문이다.** 할인되지 않은 상품 타입이 추가되었다는 것은 이후에 비슷한 요구가 발생할 수 있는 가능성이 높음을 뜻한다.


```java
public class Item {
    // 변화가되는 기능을 상위 타입에 추가
    public boolean isDiscountAvaliable() {
        return true;
    }
    ...
}

public class SpecialItem extends Item {
    @Override
    public boolean isDiscountAvailabe() {
        return false;
    }
}

public class Coupon {
    public int calculateDiscountAmount(Item item) {
        if (! item.isDiscountAvailabe()) return 0; // instanceof 연산자 사용 제거
    return item.getPrice() * discountRate;
    }
}
```
* Item 객체에 각격 할인 기능을 여부를 판단하는 기능을 추가하고 SpecialItem 에서는 기능을 재정의 했다.
* 상위 타입에 할인 여부를 확인할 수 있는 메서드를 추가해서 instanceof를 제거할 수 있게 되었다.
* **상위 타입 Item 으로만 하위 타입의 구체적인 타입을 알 필요 없이 코드구현이 가능하다.**
* OCP를 준수할수 있게 되었다. LSP를 지키지 않으면 기능을 확장하기가 어렵게된다.