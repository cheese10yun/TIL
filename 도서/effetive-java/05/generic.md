# 05 제네릭

* 자바는 1.5 부터 제네릭이라는 개념을 지원하기 시작했다.
* 제네릭이 도입전, 프로그래머는 컬렉션에 객체를 넣을 때마다 형변환 작업을 해야만 했다
* 그래서 누군가 컬렉션에 엉뚱한 자료형을 객체를 넣으면 프로그램 실행 중에 오류가 나고 만다.
* 하지만 제네릭을 사용하면 컬렉션에 넣는 객체의 자료형이 무엇인지 컴파이얼ㄹ에게 알릴 수 있다.
* 형변환 코드는 컴파일러가 알아서 넣어 줄 것이고, 잘못된 자료형의 객체를 컬렉션에 넣으려는 시도는 컴파일 과정에서 차단된다.
* 드 덕에 프로그램은 더 안전해지고 명료 해졌다. 하지만 프록램이 복잡해지는 단점도 있다
* 이번 파트에서는 제네릭의 혜택을 최대한 누리면서 복잡함은 피하는 방법을 살펴 본다. 
* **타입 세이프를 위하 나왔다라고 생각한다**


## 제네릭이란 ?

제네릭은 클래스 내부에서 사용할 데이터 타입을 외부에서 지정하는 기법을 의미한다.

![](https://s3.ap-northeast-2.amazonaws.com/opentutorials-user-file/module/516/2136.png)

## sample code
```java
package org.opentutorials.javatutorials.generic;
class EmployeeInfo{
    public int rank;
    EmployeeInfo(int rank){ this.rank = rank; }
}
class Person<T, S>{
    public T info;
    public S id;
    Person(T info, S id){ 
        this.info = info;
        this.id = id;
    }
    public <U> void printInfo(U info){
        System.out.println(info);
    }
}
public class GenericDemo {
    public static void main(String[] args) {
        EmployeeInfo e = new EmployeeInfo(1);
        Integer i = new Integer(10);
        Person<EmployeeInfo, Integer> p1 = new Person<EmployeeInfo, Integer>(e, i);
        p1.<EmployeeInfo>printInfo(e);
        p1.printInfo(e);
    }
}
```
* 생성자를 통해서 제네릭을 이용 가능
* 제네릭에서 extends를 이용해서 타입 세이프 하게 받을 수 있다.
    * 제네릭을 쓰면 모든 자료형을 받을 수 있으니, extends를 사용해서 동일부모를 갖는 자료형을 받을 수 있다.
* super로 부모를 제안 할 수도 있다는데 이건 나중에 알아보자

[생활 코딩 참고](https://opentutorials.org/module/516/6237)

## 제네릭 