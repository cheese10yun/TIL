> 해당 글은 [Kotlin in Action](http://www.yes24.com/Product/Goods/55148593?Acode=101)을 정리한 내용입니다.


# 01 코틀린이란 무엇이며 왜 필요한가 ?

코틀린은 자바 플랫폼에서 돌아가는 새로운 프로그래밍 언어다. 코틀린은 간결하고 실용적이며, 자바 코드와의 상호운용성을 중시한다. 현재 자바가 사용 중은 곳이라면 거의 대부분 코틀린을 활용할 수 있다. 

코틀린은 주목적은 현재 자바가 사용되고 있는 모든 용도에 적합하면서도 더 간결하고 생상적이며 안전한 대체 언어를 제공하는 것이다.

## 정적 타입 지정 언어
코틀린도 정적 타입 지정 언어디. 정적 타입 지정이라는 말은 모든 프로그램 구성 요소의 타입을 컴파일 시점에서 알 수 있고 프로그램 안에서 객체 필드나 메서드를 사용할 떄마다 컴파일러가 타입을 검증해준다는 뜻이다. 동적 타입 지정 언어에서는 타입과 관계 없이 모든 값을 변수에 넣을 수 있고, 메서드나 필드 접근에 대한 검증이 실행 시점에 일어나며, 그에 따라 코드가 더 짧아지고 데이터 구조를 더 유연하게 생성하고 사용할 수 있다. 하지만 컴파일 시 걸러내지 못하고 실행 시점에 오류가 발생한다.

한편 자바와 달리 코틀린에서는 모든 변수의 타입을 프로그래머가 직접 명시할 필요가 없다. 코틀린은 컴파일러가 문매긍로 부터 변수 타입을 자동으로 유추할 수 있기 때문이다.


정적 타입의 지정의 장점은 다음과 같다.
* 성능 - 실행 시점에 어떤 메서드를 호출할지 알아내는 과정이 필요 없음으로 메소드 호출이 더 빠르다.
* 신뢰성 - 컴파일러가 프로그램 정확성을 검증하기 때문에 실행 시 프로그램 오류로 중단될 가능성이 더 적어진다.
* 유지보수 - 코드에서 다르는 객체가 어떤 타입에 속하는지 알수 있기 때문에 처음 보는 코드를 다룰 떄도 더 쉽다.
* 도구 지원 - 정적 타입 지정을 활용하면 더 안전하게 리팩토링 할 수 있고, 도구는 더 정확한 코드 완성 기능을 제공할 수 있으며, IDE의 다른 지원 기능도 더 잘 만들수 있다.

## 간결성
코틀린을 만들면서 프로그래머가 작성하는 코드에서 의미가 없는 부분을 줄이고, 언어가 요구하는 구조를 만들 만족시키기 위해 별 뜻 없지만 프로그램에 꼭 넣어야하는 부수적인 요소를 줄이기 위해 많은 노력을 기울렸다. getter, setter 생성자 파라미터를 필드에 대입하기 위한 로직 등 자바에 존재하는 여러 가지 번거로운 준비 코드를 코틀린은 묵시적으로 제공하기 때문에 코틀린 코드는 그런 준비 코드로 인해 지저분 해지는 일이 없다.


## 안전성
코틀린은 JVM에서 실행한다는 사실은 이미 상당한 안전서응ㄹ 보장할 수 있다는 뜻이다. 코틀린은 타입 시스템은 null이 될수 없는 값을 추적하며, 실행 시점에 NullPointerException이 발생할 수 있는 연산을 사용하는 코드를 금지한다. 

```kotlin
var s2: String? = null // null이 될 수 있음
var s2: String = "" // null이 될 수 없음
```

## 코틀린 빌드 과정

![](../assets/kotlin-complie-flow.png)

코틀린 컴파일러로 컴파일한 코드는 코틀린 런타임 라이브러리에 의존 한다.


# 02 코틀린 기초

* 함수를 최상위 수준에 정의할 수 있다. 반드시 클래스 안에 함수를 넣어야 할 필요가 없다.
* `System.out.println` 대신에 `println()`이라고 쓴다. 코틀린 표준 라이버러리는 여러 가지 표준 자바 라이브러리 함수를 한겨랗게 사용할 수 있게 감싼 래퍼를 제공한다.
  
> 문(statement)과 식(expression)의 구분
> 코틀린에서 if는  식이지 문이아니다. **식은 값을 만들어 내며 다른 식의 하위 요소로 계산에 참여**할 수 있는 반면에 문은 자신을 둘러싸고 있는 가장 안쪽 블록의 최상위 요소로 존재하며 **아무런 값을 만들어내지 않는다.**

## 변수

```kotlin
val answer = 42 // 타입을 명시 하지 않아도 됨
val answer: Int = 42 // 타입명시 
val yeaerToCompute = 7.5e6
```
타입을 지정하지 않으면 컴파일러가 초기화 식을 분석해서 초기화 식의 타입 변수 타입으로 지정한다.

```kotlin
var result = 10; // 변경 가능한 참조를 저장
val result = 10; // 변경 불가능한 참조를 저장

val str = "String"
str.add("string") // 값변경 가능
```
`val` 참조는 자체는 불면일지라도 그 참조가 가리키는 객체의 내부 값은 변경될 수 있다.

## 문자열 템플릿

```kotlin
fun main(args: Array<String>) {
    val name = if(agrs.szie > 0) args[0] else "kotlin"
    println("Hello, $name!")
}
```
인자가 없는 경우, 있는 경우에 따라 간단한 문자열 템플릿 만들수 있다.



## 클래스와 프로퍼티
클래스라는 개념의 목적은 데이터를 캡슐화하고 캡슐화한 데이터를 다루는 코드를 한 주체 아래에 가두는 것이다. 자바에서는 필드와 접근자를 한데 묶어 프로퍼티라고 부른다.


### 프로퍼티
```kotlin
class Person (

    val name: Strung, // 읽기 전용 프로퍼티로, 코틀링은 비공개 필드와 필드를 읽는 단순한 공개 게터를 말들어 낸다.
    var isMarrried: Boolean // 읽기, 쓰기 프로퍼티로, 코틀린은 (비공개) 필드, 공개 개터, 공개 세터를 만들어 낸다.
)

fun main(args: Array<String>){
    val person = Person("name", true)
    println(person.name) // name
    println(person.isMarried) // true
}
```

### 커스텀 접근자
```kotlin
class Rectangle(val height: Int, val width: Int) {

    val isSquare: Boolean
        get() { // 프로퍼티 게터 선언
            return height == width
        }
}
```
 
## 선택 표현과 처리: enum, when

### enum 클래스
```kotlin
 enum class Color(
    val r: Int, val g: Int, val b: Int
) {
    RED(255, 0, 0),
    ORANGE(255, 165, 0),
    YELLOW(255, 255, 0),
    GREEN(0, 255, 0),
    BLUE(0, 0, 255); // 코틀린에서 유일하게 `;` 이 필요한 곳이 enum이다.

    fun rgb() = (r * 256 * g) * 256 + b
}
```
코틀린에서 enum은 **소프트 키워드**라 부르는 존재다. enum은 class 앞에 있을 떄 특별한 의미를 지니지만 **다른 곳에서 이름에 사용할 수 있다**. 반면 클래스는 키워드다. 따라서 class라는 이름을 사용할 경우 clazz나 aClass라는 이름을 사용 한다.

### when으로 enum 클래스 다루기
```kotlin
fun getMnemonic(color: Color) = // 함수 반환 값으로 when 식을 직접 사용 가능
    when (color) {
        Color.RED -> "Richard"
        Color.ORANGE -> "Of"
        Color.YELLOW -> "York"
        Color.GREEN -> "Gave"
        Color.BLUE, Color.INDIGO -> "Battle"
    }
```
자바의 switch에 해당하는 것이 코틀린의 when이다. if와 마찬가지로 **when도 값을 만들어내는 식이다.** 따라서 식이 본문인 함수에 when을 바로 사용할 수 있다.(식이니 값을 가질수 있기 때문에) 자바와 달리 각 분기의 끝에 break를 넣지 않아도 된다.



### when과 임이의 객체를 함께 사용
코틀린에서 when은 자바의 switch보다 훨씬 더 강력하다. 분기 조건에 상수(enum 상수나 숫자 리터럴)만을 사용할 수있는 자바 switch와 달리 코틀린 when의 분기 조건은 임이의 객체를 허용 한다.

```kotlin
fun mix(c1: Color, c2: Color) =
    when (setOf(c1, c2)) {
        setOf(Color.RED, Color.YELLOW) -> Color.ORANGE
        setOf(Color.YELLOW, Color.BLUE) -> Color.GREEN
        else -> throw IllegalArgumentException("argument is invalid")
    }
```
c1, c2가 RED, YELLOW라면 그 둘을 혼합한 결과는 ORANGE다. 이를 구현해서 집합 비교를 사용한다.

**when의 분기 조건 부분에 식을 넣을 수 있기 때문에 많은 경우 코드를 더 간결하고 아름답게 작성 할 수 있다.**


### 스마트 캐스트: 타입 검사와 타입 캐스트를 조합
```koltin
fun eval(e: Expr): Int =
    when (e) {
        is Num -> e.value
        is Sum -> eval(e.right) + eval(e.left)
        else -> throw IllegalArgumentException("unknown")
    }
```
**어떤 변수나 원하는 타입인자를 일단 is로 검사하고 나면 굳이 변수를 원하는 타입으로 캐스팅 하지 않아도 마치 처음부터 그 변수가 원하는 타입으로 선언된 것처럼 사용할 수 있다.** 하지만 실제로는 컴파일러가 캐스팅을 수행해준다. 이를 **스마트 캐스트**라고 한다.

## 대상을 이터레이션: while과 for 루프

### 수에 대한 이터레이션: 범위와 수열
루프의 가장 흔한 용례인 초깃값, 증가 값, 최종 값을 사용한 루프를 대신하기 위해 코틀린에서는 범위를 사용한다.

범위는 기본적으로 두 값으로 이뤄진 구간이다. 보통 그 두 값을 정수 등의 숫자 타입이며, `..` 연산자로 시작 값과 끝 값을 연결해서 범위를 만든다.

```kotlin
fun main(args: Array<String>) {

    val oneToTen = 1..10

    println(oneToTen)

    for (i in 1..100){
        println(fizzBuzz(i))
    }

    for (i in 100 downTo 1 step 2){
        println(fizzBuzz())
    }
}

fun fizzBuzz(i: Int) = when {
    i % 15 == 0 -> "FizzBuzz"
    i % 3 == 0 -> "Fizz"
    i % 5 == 0 -> "Buzz"
    else -> "$i"
}
```

### 맵에 대한 이터레이션
```kotlin
    val binaryReps = TreeMap<Char, String>()

    for (c in 'A'..'F') { // A ~ F까지 이터레이션
        val binary = Integer.toBinaryString(c.toInt()) // 아시크 코드를 2진표현으로 
        binaryReps[c] = binary // c를 키로 c의 2진 표현을 맵에 put
    }

    for ((letter, binary) in binaryReps) { // 맵에 대한 이터레이션, 맵의 키와 값을 두 변수에 각각 대입한다.
        println("$letter = $binary")
    }
```

키를 사용해 맵의 값을 가져어고나 키에 해당 하는 값을 넣는 작업인 get, put을 사용 하는 대신에 `map[key]` `[key] = value`

### in으로 컬렉션이나 범위의 원소 검사
```kotlin
fun isLetter(c: Char) = c in 'a'..'z' || c in 'A'..'Z'

fun isNotDigit(c: Char) = c !in '0'..'9'
```
`in` 연산자를 사용해 어떤 값이 범위에 속하는지 검사할 수 있다. 반대로 `!in`을 사용하면 어떤 값이 범위에 속하지 않는지 검사 할 수 있다.


```kotlin
fun reconize(c: Char) = when (c) {
    in '0'..'9' -> "number"
    in 'a'..'z', in 'A'..'Z' -> "string"
    else -> "what ? "
}
```

## 코틀린의 예외 처리
코틀린의 예외처리 방식은 자바와 비슷하다. 발생한 예외를 함수 호출 단에서 처리(catch)하지 않으면 함수 호출 스택을 거슬러 올라가면서 예외를 처리하는 부분이 나올 때까지 예외를 다시 던진다 (throw)



### try, catch, finally
```kotlin
fun readNumber(reader: BufferedReader): Int?{
    try {
        val line = reader.readLine()
        return Integer.parseInt(line)
    } catch (e: NumberFormatException){
        return null
    } finally {
        reader.close()
    }
}
```
자바 코드와 가장 큰 차이는 **throw 절이 코드에 없다는 점이다.** 자바에서는 체크 예외인 경우에는 함수를 작성할 때 함수 선언 뒤에 throw IOExecption을 붙여야 한다. **자바에서는 체크 예외를 명시적으로 처리해야 한다.**

**코틀린은 체크 예외외 언체크 예외를 구분하지 않는다.** 자바는 체크, 언체크 예외를 의미적으로 구분짓지만 실제 개발에서는 이것을 의미 있게 구분하지 않거니와 체크드 예외를 `try catch` 으로 감싸고 다음 로직을 이어가는 코드를 작성하는 경우도 흔하게 있다. 이런 부분들 때문에 예외를 처리하는데 불편함과, 실수를 발생시킨다.




## try를 식으로 사용
```kotlin
fun readNumber (reader: BufferedReader) {
    var number = try {
        Integer.parseInt(reader.readLine())
    } catch (e: NumberFormatException) {
        return
        // null catch 블록도 값으로 만들면 그 다음 동작이 실행된다.
    }
    println (number) // catch 블록 다음이기 때문에 이 코드는 실행되지 않는다.
}
```
코틀린의 try 키워드는 if when과 마찬가지로 **식이다**. 따라서 try의 값을 변수에 대입할 수 있다. 

위 예제처럼 **catch 블록안에서 return문을 사용한다.** 예외가 발생한 경우 catch 블록 다음의 코드는 실행되지 않는다. 