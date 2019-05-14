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


* `public void doSomething(T p){...}` **에서 T 타입인지 S 타입인지 알필요 없이 돌아가야 한다.**
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
