그동안 개인 블로그에만 포스팅을 해왔던 인텔리제이의 기능 중에서 가장 많이 사용하는 기능들을 한 번 더 정리했습니다. 이전 인텔리제 포스팅은 [GitHub](https://github.com/cheese10yun/IntelliJ)에서 확인할 수 있습니다.


# 인텔리제이 리팩토링 기능

![](https://i.imgur.com/Dq0m2Pf.png)

인텔리제이의 리팩토링 기능을 이용해서 비교적 쉽게 리팩토링을 할 수 있습니다. 대표적인 리팩토링 기능들을 하나 하나 천천히 살펴 보도록 하겠습니다.

**해당 기능의 단축키는 스크린샷 하단 녹색으로 표시되어있습니다. 단축키 환경이 다른 사용자들은 해당 그림 처럼 Find Action 창에서 리팩토링 키워드를 검색하면 원하는 기능을 쉽게 찾을 수 있습니다. 위 예제는 `Extract Method` 검색한 이미지 입니다.**


## Extract Method : 함수 수출 기능

![](https://i.imgur.com/8uhaWJF.png)

```java
if(age > 19){
    //메소드 수출전
    System.out.println("성인 입니다.");
}

if(isSenior(age)){
    //메소드 수출후
    System.out.println("성인 입니다.");
}

private boolean isSenior(int age) {
    return age > 19;
}

```

메소드로 수출하는 기능으로 가장 많이 사용하는 리팩토링 기능입니다. 단순히 `age > 19` 으로 구분하는 것보다 `isSenior` 메소드를 이용해서 묻는 것이 가독성 및 유지보수에서도 좋다고 생각합니다.
저는 테스트 코드를 통과하는 코드를 최대한 빠르게 만들고 인텔리제이의 리팩토링 기능을 통해서 클린코드 작업을 진행합니다.

## Change Signature : 메소드 파라메터 추가, 삭제 및 변경
![](https://i.imgur.com/bsAUiSX.png)

```java
//리팩토링 전
public void printName() {
    printName("Yun");
}

private String printName(String firstName) {
    return firstName;
}

//리팩토링 후
public void printName() {
    printName("Yun", "kim");
}

private String printName(String firstName, String lastName) {
    return firstName + lastName;
}
```

메소드에 파라미터가 추가되는 일은 빈번하게 만나게 됩니다. 이 때 유용하게 사용할수 있는 기능이 `Change Signature` 기능입니다.  또한 메소드에 추가된 파라메터는 해당 메소드를 호출하는 곳에 일괄적용 됩니다.

## Rename : 이름 변경

![](https://i.imgur.com/N4RVP73.png)

```java
public void test() {
    printName("Yun", "kim");
}

//리팩토링전
private String printName(String firstName, String lastName) {
    return firstName + lastName;
}

//리팩토링후
private String printFullName(String firstName, String lastName) {
    return firstName + lastName;
}
```
클래스, 메소드, 변수 등등 이름을 변경하는 기능입니다. 정말 많이 사용 하는 기능중에 하나입니다. 이름이 변경되면 참조되는 모든 곳에서 일괄 변경 됩니다.

## Extract Variable : 변수 수출 기능
![](https://i.imgur.com/gIBsKZ8.png)

```java
// 리팩토링 전
public void printFullName() {
    printFullName("Yun", "kim");
}

//리팩토링 후
public void printFullName() {
    final String yun = "Yun";
    printFullName(yun, "kim");
}
```

특정 값을 변수로 수출하는 기능입니다. 기본적으로 `final` 키워드로 변수가 할당됩니다.변수는 생각보다 변수보다 상수로 사용을 많이 됩니다. 즉 한번 할당된 값을 변경하는 생각보다 흔하지는 않습니다.  그렇기 때문에 저는 일단 상수로 선언하고 나중에 값을 변경할 이유가 생기면 그때 `final` 키워드를 제거하는 방향으로 사용하고 있습니다.  `final` 키워드는 상수로 할당된 값이 변경되지 않는다고 명시적으로 선언함으로써 코드를 이해하는데 좋은 역활을 한다고 생각합니다.

## Extract Filed : 맴버 필드 수출 기능

![](https://i.imgur.com/OFRC3sm.png)

변수 수출 기능과 거의 동일 합니다. 특정 값을 멤버 필드로 할당됩니다.

## Extract Constant

![](https://i.imgur.com/09d6IB8.png)

변수 수출 기능과 거의 동일 합니다. 특정 값을 static 영역에 할당합니다.

## Pull Members up

![](https://i.imgur.com/8UDFcwU.png)

```java

public interface AttackStrategy {
}

public class MissileStrategy implements AttackStrategy{
    public void attack() {
        System.out.println("미사일 공격");
    }
}
```

위와 같은 코드일 경우 `attack()` 메소드를 상위 인터페이의 추상 메소드로 올리는 기능 입니다. 하위 클래스에서는 `@Override` 어노테이션이 자동으로 추가됩니다.

## Push Members down

Pull Members up 기능의 반대 기능입니다. 상위 클래스의 있는 메소드를 하위 클래스의 메소드로 내려줍니다.


# Git 사용법
벡엔드 개발을 할 때 많은 틀을 사용합니다. Source Tree, Postman, Sequel Pro 등등 수없이 개발하다가 해당툴을 사용하기 위해서 왔다 갔다 하는 경우가 많습니다. 하지만 외부 툴을 사용하는 순간 뭔가 개발 흐름이 끊기는 느낌이 있어서 저는 거의 모든 작업을 인텔리제이에서 하는 것을 지향합니다. **이번 포스팅은 Source Tree 즉 Git 관련 툴을 대체하는 방법을 소개하겠습니다.** ***해당 기능의 단축키는 스크린샷 하단에 표시되있습니다.***

## Commit
![](https://i.imgur.com/6xFlHcv.png)

* Diff : 변경 이력이 있는 모든 파일에 대해서 Diff 기능을 제공합니다. Commit을 하기 전에 자신이 변경한 모든 파일에 대해서 한 번 꼼꼼하게 읽어 보는 습관을 갖는 것이 실수를 줄이는 가장 효율적인 방법이라고 생각합니다.
* Before Commit : **인텔리제이로 커밋을 했을 경우 얻을 수 있는 최고의 장점이라고 생각합니다. **간단하게 다루고 이 기능은 추후 포스팅을 통해서 다시 한번 다루겠습니다.
    * Check TODO:  //TODO 메시지가 남아 있을 경우 사용자에게 해당 TODO가 남아 있다고 알려줍니다.
    * Optimize imports : 사용하지 않은 imports를 제거해 줍니다.
    * Reformat Code : 인텔리제이에서 설정한 코드스타일로 변경 해줍니다.

## Push
![](https://i.imgur.com/TzF1dNk.png)
* 커밋한 내용을 레파지토리에 푸쉬 하는 기능입니다.

## Pull
![](https://i.imgur.com/pMhiJPi.png)
* Remote Repository에 변경 내역을 Pull 받는 기능입니다.

## Stash
![](https://i.imgur.com/ZIn63MI.png)

* Stash 기능은 아주 간단하게 소개하면 변경 내역을 임시저장 해놓는 기능입니다. 가령 다른 branch로 변경을 원할 때 변경 내역이 남아 있어 부득이하게 commit을 하는 경우가 있습니다. 그럴 때 stash 임시 저장소에 저장하면 편리하게 branch 간의 이동을 할 수 있습니다.
* 기본 단축키가 지정되있지 않아 `Find Action` 으로 stash 기능을 사용합니다.

## UnStash
![](https://i.imgur.com/kDCYgib.png)
* Stash 저장한 내역을 POP 하는 기능입니다.
* 기본 단축키가 지정되있지 않아 `Find Action` 으로 unStash 기능을 사용합니다.
* 저장한 메시지를 기반으로 선택하고 `Apply Stash`을 클릭하면 변경 내역을 불러옵니다.

## Share Project on Github
![](https://i.imgur.com/bOJ0X8n.png)
* 해당 프로젝트를 Github에 올리는 기능입니다.

## Branch
![](https://i.imgur.com/HJjPhjW.png)
* New branch, Checkout, Compare, Merge, Rebase 등등 다양한 기능들을 제공합니다.


개발할 때 생각나는 사소 한 것들을 이슈 트래킹이나 메모장 같은 곳에 작성하기엔 번거롭고 올려 등록해도 잘 찾아보지 않는 경우가 많습니다. 그래서 저 같은 경우는 아래 그림과 같이 생각나는 모든 것들을 todo로 갈겨 놓습니다. 저는 개발 할 때 IDE에서 벗어나는 걸 싫어해서 웬만한 것들 IDE에서 작업을 진행합니다. 이러한 문제를 해결하는 인텔리제이 팁을 포스팅해보겠습니다.

# 인텔리제이 Todo 활용법
개발할 때 생각나는 사소 한 것들을 이슈 트래킹이나 메모장 같은 곳에 작성하기엔 번거롭고 올려 등록해도 잘 찾아보지 않는 경우가 많습니다. 그래서 저 같은 경우는 아래 그림과 같이 생각나는 모든 것들을 todo로 갈겨 놓습니다. 저는 개발 할 때 IDE에서 벗어나는 걸 싫어해서 웬만한 것들 IDE에서 작업을 진행합니다. 이러한 문제를 해결하는 인텔리제이 팁을 포스팅해보겠습니다.


![](https://i.imgur.com/74N8oYR.png)

위 그림과 같이 생각 나는 것들을 날짜 + 내용 + 작성자 이름 형식으로 Todo로 등록합니다. 날짜 정보와 누가 작성했는 지 는 나중에 Todo를 추적할 때 많은 도움을 줍니다.


## TODO 전체 검색

![](https://i.imgur.com/AgvhG70.png)

개발 중에 Todo 전레 검색을 통해서 혹시라도 놓친 작업들이 있나 검색해 볼 수 있습니다. 단축키는 스크린샷에 나와 있습니다.

## 커밋할 때 한 번 더 검사

![](https://i.imgur.com/ZTW8jIq.png)

인텔리제이를 통해서 커밋 작업을 진행하게 되면 다양한 지원을 받을 수 있습니다. 그중에 하나가 TODO 검사 입니다. 왼쪽 중단에 `Check TODO` 라는 항목을 체크 하고 커밋을 하게 되면 아래 그림과 같이 표시 됩니다.

![](https://i.imgur.com/x5yMIgW.png)

작성된 TODO가 있으면 커밋전에 사용자에게 알려줍니다. 남은 작업들을 확인하려면 `Review` 버튼을 누를 경우 위 처럼 작성된 TODO 리스트들이 출력됩니다. 혹시라도 잊어 버린 TODO를 커밋전에 확인 할 수 있습니다. `Before Commit` 기능은 이 이외도 많은 기능이 있는데 추후 포스팅에서 정리 해보겠습니다.


# 문자열 팁

## 동일 문자열 변경

![⌘ + ⌃ + g](https://i.imgur.com/fAMA6Or.gif  "⌘ + ⌃ + g")
***⌘ + ⌃ + g***


코드를 작성하다가 동일한 문자열을 변경해야 하는 경우는 빈번하게 발생합니다. 위 그림과 같이 특정 변수를 모두 변경해야 하는 경우가 대표적인 예입니다. 변수가 2~3이면 귀찮지 않은데 위 와 같은 경우에는 크게 귀찮아집니다. 이럴 때 유용하게 사용하는 기능입니다.

변경하고 싶은 문자열을 드래그 이후 ***command + control + g***  단축키를 입력하면 동일 문자열을 한 번에 변경이 가능합니다.

## 동일한 위치 문자열 변경
![⌥  + dra](https://i.imgur.com/Z5IYg6w.gif  "⌥  + drag")
***⌥  + drag***

동일 문자열과 비슷한 기능입니다. 만약 변경 대상이 동일한 문자열이 아닐 경우도 위와 같이 발생할 수 있습니다. 이러한 경우에 **Alt + 마우스 드래그**를 통해서 한 번에 변경하실 수 있습니다. 이 기능 익숙해지면 다양하게 사용할 수 있습니다.

## 복사 리스트
![⌘  + ⇧ + v ](https://i.imgur.com/6ClfGhw.gif)

개발을 하면서 우리는 수없이 정말 수없이 복사 붙여 넣기를 합니다... 이럴 때 가장 불편한 점 중에 하나는 새로 복사할 때마다 이전 복사한 내용들이 없어진다는 점이죠.


이러한 경우에 ***command + shift + v***를 사용하시면 간단하게 해결됩니다. 단축키를 입력하시면 복사한 리스트들 출력되고 해당 리스트에서 엔터키를 입력하면 됩니다
## Live Template
![console.log()](https://i.imgur.com/ojSSHaC.gif)
![console.log()](https://i.imgur.com/SPMRe1P.gif)


코드를 작성하다 보면 중간중간 결과 값을 확인하기 위해서 `console.log('')`를 사용해서 확인 작업을 수없이 진행하게 됩니다. 이러한 반복적인 코드와 예약 어를 정의하고 예약 어를 통해서 코드를 불러오는 기능이 Live Template입니다.

저 같은 경우에는 예약어 clog로 `console.log('')`를 간단하게 작성합니다. 물론 이러한 간단한 코드는 그때그때 작성해도 크게 불 펴하지는 않지만 코드량이 길어지면 점점 귀찮아지기 시작합니다.

api를 호출하는 ajax 같은 코드도 빈번하게 작성되는 코드다 보니 위 그림과 같이 ajg 예약어로  ajax get 코드를 간단하게 작성하실 수 있습니다.

하지만 저 같은 신입의 경우 해당 코드를 어느 정도 숙지가 완료되었을 때 Live Template에 추가하는 것을 권장드립니다. 코드를 숙지하지 않고 IDE기능을 통해서만 작성하는 것은 좋은 습관은 아니라고 생각합니다.

## Live Template 등록 방법
![Live Template](https://i.imgur.com/SPMRe1P.gif)

**⌘ + ,단축키**를 통해서 설정에 들어가시면 Live Template 항목에서 해당 언어의 Template을 설정할 수 있습니다. 자바스크립트뿐만 아니라 다양하 언어의 Template 지정할 수 있습니다. ***반드시 하단에 Define을 클릭하시고 해당 언어를 다시 한번 정의해주셔야 됩니다.***