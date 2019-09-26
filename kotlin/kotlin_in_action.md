> 해당 글은 [Kotlin in Action](http://www.yes24.com/Product/Goods/55148593?Acode=101)을 정리한 내용입니다.


# 01 코틀린이란 무엇이며 왜 필요한가 ?

코틀린은 자바 플랫폼에서 돌아가는 새로운 프로그래밍 언어다. 코틀린은 간결하고 실용적이며, 자바 코드와의 상호운용성을 중시한다. 현재 자바가 사용 중은 곳이라면 거의 대부분 코틀린을 활용할 수 있다. 코틀린은 주목적은 현재 자바가 사용되고 있는 모든 용도에 적합하면서도 더 간결하고 생상적이며 안전한 대체 언어를 제공하는 것이다.

## 정적 타입 지정 언어
코틀린도 정적 타입 지정 언어디. 정적 타입 지정이라는 말은 모든 프로그램 구성 요소의 타입을 컴파일 시점에서 알 수 있고 프로그램 안에서 객체 필드나 메서드를 사용할 때마다 컴파일러가 타입을 검증해준다는 뜻이다. 동적 타입 지정 언어에서는 타입과 관계 없이 모든 값을 변수에 넣을 수 있고, 메서드나 필드 접근에 대한 검증이 실행 시점에 일어나며, 그에 따라 코드가 더 짧아지고 데이터 구조를 더 유연하게 생성하고 사용할 수 있다. 하지만 컴파일 시 걸러내지 못하고 실행 시점에 오류가 발생한다.

한편 자바와 달리 코틀린에서는 모든 변수의 타입을 프로그래머가 직접 명시할 필요가 없다. 코틀린은 컴파일러가 문매긍로 부터 변수 타입을 자동으로 유추할 수 있기 때문이다.


정적 타입의 지정의 장점은 다음과 같다.
* 성능 - 실행 시점에 어떤 메서드를 호출할지 알아내는 과정이 필요 없음으로 메소드 호출이 더 빠르다.
* 신뢰성 - 컴파일러가 프로그램 정확성을 검증하기 때문에 실행 시 프로그램 오류로 중단될 가능성이 더 적어진다.
* 유지보수 - 코드에서 다르는 객체가 어떤 타입에 속하는지 알수 있기 때문에 처음 보는 코드를 다룰 때도 더 쉽다.
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
* `System.out.println` 대신에 `println()`이라고 쓴다. 코틀린 표준 라이버러리는 여러 가지 표준 자바 라이브러리 함수를 간결하게 사용할 수 있게 감싼 래퍼를 제공한다.
  
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
코틀린에서 enum은 **소프트 키워드**라 부르는 존재다. enum은 class 앞에 있을 때 특별한 의미를 지니지만 **다른 곳에서 이름에 사용할 수 있다**. 반면 클래스는 키워드다. 따라서 class라는 이름을 사용할 경우 clazz나 aClass라는 이름을 사용 한다.

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
```kotlin
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
루프의 가장 흔한 용례인 초깃값, 증가 값, 최종 값을 사용한 루프를 대신하기 위해 코틀린에서는 범위를 사용한다. 범위는 기본적으로 두 값으로 이뤄진 구간이다. 보통 그 두 값을 정수 등의 숫자 타입이며, `..` 연산자로 시작 값과 끝 값을 연결해서 범위를 만든다.

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

키를 사용해 맵의 값을 가져오거나 키에 해당 하는 값을 넣는 작업인 get, put을 사용 하는 대신에 `map[key]` `[key] = value`

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

# 03 함수 정의와 호출

## 코틀린에서 컬렉션 만들기

```kotlin
val hashSetOf = hashSetOf(1, 7, 53)
println(hashSetOf) // [1, 53, 7]

val arrayListOf = arrayListOf(1, 7, 53)
println(arrayListOf) // [1, 7, 53]

val hashMapOf = hashMapOf(1 to "one", 7 to "seven", 53 to "fifty-three")
println(hashMapOf) // {1=one, 53=fifty-three, 7=seven}

println(hashSetOf.javaClass) // class java.util.HashSet
println(arrayListOf.javaClass) // class java.util.ArrayList
println(hashMapOf.javaClass) // class java.util.HashMap
```
코틀린은 위 처럼 간단하게 컬랙션을 사용할 수 있다. **여기서 `to`는 언어가 제공하는 특별한 키워드가 아니라 일반 함수이다.**

`javaClass`는 자바의 `getClass()`에 해당하는 코틀린 코드다. 이 코드는 코틀린이 자신만의 컬렉션 기능을 제공하지 않는 다는 뜻이다. 

코틀린이 자체 컬렉션을 제공하지 않는 이유는 표준 자바 컬렉션을 활용하면 자바 코드와 상호작용하기가 훨씬 쉽기 때문이다. 자바에서 코틀린 함수를 호출하거나 코틀린에서 자바 함수를 호출할 때 자바와 코틀린 컬렉션을 서로 변환할 필요가 없다. **코틀린 컬렉션은 자바 컬렉션과 똑같은 클래스다.** 하지만 코틀린에서는 자바보다 더 많은 기능을 쓸 수 있다.

```kotlin
val strings = listOf("first", "second", "third")
println(strings.last()) // third

val numbers = setOf(1, 14, 2)
println(numbers.max()) // 14
```

위 코드 처럼 컬렉션에서 많은 기능들을 제공해준다.

## 함수를 호출해서 쉽게 만들기

```kotlin

fun <T> joinToString(
    collection: Collection<T>,
    separator: String,
    prefix: String,
    postfix: String
): String {
    val result = StringBuilder(prefix)
                                        
    for ((index, element) in collection.withIndex()) {
        if (index > 0) result.append(separator)
        result.append(element)
    }

    result.append(postfix)
    return result.toString()
}
return result.toString()


val hashSetOf = hashSetOf(1, 7, 53)
println(joinToString(arrayListOf, "; " ,"(",")")) // (1; 7; 53)
println(joinToString(arrayListOf, separator = "; " ,prefix = "(", postfix = ")")) // // (1; 7; 53)
```
위 코드 처럼 코틀린으로 작성한 함수를 호출할 때는 함수에 전달하는 인자 중 일부의 이름을 명시할 수 있다. 이런 코드는 가독성이 좋은 장점이 있다. **호출 시 인자 중 어느 하나라도 이름을 명시하고 나면 혼동을 막기 위해 그 뒤에 오는 모든 인자는 이름을 꼭 명시해야 한다.**

## 디폴트 파라미터 값
코틀린의 디폴트 파라미터 값을 이용하면 자바의 오버로딩이 많아 지는 문제를 해결 할 수 있다.

```kotlin
fun <T> joinToString(
    collection: Collection<T>,
    separator: String = ", ",
    prefix: String = "",
    postfix: String = ""
): String {
    val result = StringBuilder(prefix)

    for ((index, element) in collection.withIndex()) {
        if (index > 0) result.append(separator)
        result.append(element)
    }

    result.append(postfix)

    return result.toString()
}

println(joinToString(arrayListOf, separator = ", ")) // 1, 7, 53
println(joinToString(arrayListOf, separator = "; ")) // 1; 7; 53
```
함수의 디폴트 파라미터 값은 함수를 호출하는 쪽이 아니라 함수 선언 쪽에서 지정된다. 그레서 디폴트 값을 변경하면 이미 작성된 코드중에 값을 지정하지 않은 모든 인자는 자동으로 바뀐 디폴트 값으로 적용 받는다.

## 메서드를 다른 클래스에 추가: 확장 함수와 확장 프로퍼티
**확장 함수를 함수는 어떤 클래스의 멤버 메소드인 것처럼 호출할 수 있지만 그 클래스 밖에 선언된 함수다.**

```kotlin

// String = 수신 객체 타입, this. = 수신 객체 (this는 자기자신, 즉 함수를 의미한다) 
fun String.lastChar(): Char = this.get(this.length - 1);
```
확장 함수를 만들려면 추가하려는 함수 이름 앞에 그 함수가 확장할 클래스의 이름을 덧붙이기만 하면 된다. **클래스 이름을 수신 객체 타입, 확장 함수가 호출되는 대상이 되는 값을 수신 객체라고 부른다.**


### 확장 함수로 유틸리티 함수 정의
```kotlin
fun <T>Collection<T>.joinToString2(
    separator: String = ", ",
    prefix: String = "",
    postfix: String = ""
): String {
    val result = StringBuilder(prefix)

    for ((index, element) in this.withIndex()) {
        if (index > 0) result.append(separator)
        result.append(element)
    }
    result.append(postfix)
    return result.toString()
}

print(arrayListOf.joinToString2("; ", "#", "@")) // #1; 7; 53@
```
확장 함수로 유틸리티 함수를 편리하게 사용할 수 있다. **주위 해야할것은 확장 함수는 오버라이드 할 수 없다는 것이다.**

## 코드 다듬기: 로컬 함수와 확장
코틀린에서는 함수에서 추출한 함수를 원 함수 내부에 중첩시킬 수 있다. 그렇게 되면 문법적인 부가 비용을 들이지 않고도 깔끔하게 코드를 조작할 수 있다.

```kotlin
fun saveUser(user: User) {

    if (user.name.isEmpty()) {
        throw IllegalArgumentException("${user.name}: ..")
    }

    if (user.address.isEmpty()) {
        throw IllegalArgumentException("${user.address}: ...")
    }

    // user database save....
}
```
흔하게 발생하는 중복 적인 코드이다. 

```kotlin
fun saveUser2(user: User) {
    fun validate(value: String, fieldName: String) {
        if (value.isEmpty()) {
            throw IllegalArgumentException("${user.id} : empty $fieldName")
        }
    }

    validate(user.name, "name")
    validate(user.address, "Address")
}
```
로컬 함수로 분리하면 중복을 없애는 동시에 코드 구조를 깔끔하게 유지할 수 있다. **로컬 함수는 자신이 속한 바깥 함수의 모든 파라미터와 변수를 사용할 수 있다.**

```kotlin
fun User.validateBeforeSave() {

    fun validate(value: String, fieldName: String) {
        if (value.isEmpty()) {
            throw IllegalArgumentException("$id: empty $fieldName")
        }
    }

    validate(name, "Name")
    validate(address, "Address")
}

fun saveUser4(user: User) {
    user.validateBeforeSave()
}
```

이 경우 검증 로직은 User를 사용하는 다른 곳에서 쓰이지 않을 기능이기 때문에 User에 포함시키고 싶지는 않고 User를 간결하게 유지하려면 검증 로직을 확장 함수로 작성할 수 도 있다.

# 04 클래스, 객체, 인터페이스
코틀린의 클래스와 인터페이스는 자바 클래스, 인터페이스와는 약간 다르다. 예를들어 인터페이스에 프로퍼티 선언이 들어갈 수 있다. 중첩 클래스는 기본적으로 내부 클래스가 아니다. 즉, 코틀린 중첩 클래스에는 외부 클래스에 대한 참조가 없다. 코틀린에서 클래스를 data로 선언하면 컴파일러가 일부 표준 메서드를 생성해준다. 

## 클래스 계층 정의

### 코틀린 인터페이스
코틀린 인터페이스 안에는 추상 메서드 뿐 아니라 구현이 있는 메서드도 정의할 수 있다. (자바8의 Default Method 와 비슷) 다만 인터페이스에는 아무런 상태도 들어갈 수 없다.


```kotlin
interface Clickable {
    fun click()
}

class Button : Clickable {
    override fun click() = println("I was clicked")
}
```
자바에서는 extends와 Implements 키워드를 사용하지만, **코틀린에서는 클래스 이름 뒤에 콜론`:`을 붙이고 인토페이스와 클래스 이름을 적는 것으로 클래스 확장과 인터페이스 구현을 모두 처리한다.**

자바의 `@Override` 애노테이션과 비슷한 `override` 변경자는 상위 클래스나 상위 인터페이스에 있는 프로퍼티나 메서드를 오버라이드한다는 표시이다. **코틀린에서는 `override`는 반드시 사용해야한다.** `override` 변경자는 실수로 상위 클래스의 메서드를 오버라이드하는 것을 방지해준다.


### open, final, abstract 변경자: 기본적으로 final
자바에서는 final로 명시적으로 상속을 금지하지 않은 모든 클래스를 다른 클래스가 상속 수 있다. 이러한 경우 많은 문제가 있다. 취약한 기반 클래스 라는 문제는 하위클래스가 기반 클래스에 대해 가졌던 가정이 기반 클래스를 변경함으로써 깨져버린 경우에 생긴다. **어떤 클래스가 자신을 상속하는 방법에 대한 정확한 규칙(어떤 메소드를 어떻게 오버라이드 해야하는 지 등)을 제공허지 않는다면 그 클래스의 클라이언트는 기반 클래스를 작성한 사람의 의도와 다른 방식으로 오버라이드할 위험이 있다.**

**Effective Java에서는 상속을 위한 설계와 문서를 갖추거나, 그럴 수 없다면 상속을 금지하라는 조언을 한다. 이는 하위 클래스에서 오버라이드하게 의도된 클래스와 메소드가 아니라면 모두 final로 만들라는 뜻이다.**



```kotlin
open class RichButton : Clickable {

    fun disable() {} // 이 함수는 final이다. 하위 클래스가 이 메서드를 오버라이드할 수 없다.
    
    open fun animate() {} // 이 함수는 open이다. 하위 클래스에서 오버라이드 할 수 있다. 
    
    override fun click() {} // 이 함수는 열려있는 메서드를 오버라이드한다. 오버라이드한 메서드는 기본적으로 open 이다. 오버라이드하는 메서드를 구현을 하위 클래스에서 오버라이드 못하게 금지하려면 final 명시해야 한다.
}
```

```kotlin
abstract class Animated { // 이 클래스는 추상클래스다. 이 클래스의 인스턴스를 만들 수 없다.

    abstract fun animate() // 이함수는 추상 함수다. 이 함수에는 구현이 없다 하위 클래스에서는 이 함수를 반드시 오버라이드해야 한다.

    open fun stopAnimating() {} // 추상 클래스에 속했더라도 비추상 함수는 기본적으로 final이지만 open으로 오버라이드를 허용할 수 있다.

    fun animateTwice() {} // 추상 클래스 함수는 기본적으로 final이다.

}
```
인터페이스 멤버의 경우 final, open, abstract를 사용하지 않는다. 인터페이스 멤버는 항상 open 이며, final으로 변경할 수 없다.

변경자      | 이 변경자가 붙은 멤버는...               | 설명
---------|--------------------------------|------------------------------------------------------------
final    | 오버라이드할 수 없음                    | 클래스 멤버의 기본 변경자다.
open     | 오버라이드 할 수 있음                   | 반드시 open을 명시해야 오버러이드할 수 있다.
abstract | 반드시 오버라이드 해야함                  | 추상 클래스의 멤버에만이 변경자를 붙일 수 있다. 추상 멤버에는 구현이 있으면 안된다.
override | 상위 클래스나 상위 인스턴스의 멤버를 오버라이드하는 중 | 오버라이드하는 멤버는 기본적으로 열려있다. 항위 클래스의 오버러이드를 금지하려면 final을 명시해야한다.

### 가시성 변경자: 기본적으로 공개
기본적으로 코틀린 가시성 변경자는 자바와 비슷하다. 자바와 같은 public, protected, private 변경자가 있다. 하지만 코틀린의 기본 가시성은 자바와 다르다. 아무 변경자도 없는 경우 모두 public이다.

**자바의 기본 가기성인 패키지 전용 pckage-private은 코틀린에 없다. 코틀린은 패키지를 네임스페이스를 관리하는 용도로만 사용한다. 그래서 패키지를 가시성 제어에 사용하지 않는다.**

변경자            | 클래스 멤버              | 최상위 선언
---------------|---------------------|-------------------
public(기본 가시성) | 모든 곳에서 볼 수 있다.      | 모든 곳에서 볼 수 있다.
internal       | 같은 모듈 안에서만 볼 수 있다.  | 같은 모듈 안에서만 볼 수 있다.
protected      | 하위 클래스 안에서만 볼 수 있다. | 최상위 선언에 적용할 수 없다.
private        | 같은 클래스 안에서만 볼 수 있다. | 같은 파일 안에서만 볼 수 있다.


```kotlin
internal open class TalkativeButton {
    private fun yell() = println("Hey!")

    protected fun whisper() = print("Let talk")
}

fun TalkativeButton.giveSpeech() { // 오류 : pulbic 멤버가 자신의 internal 수신 타입인 TalkativeButton을 노출함
    yell() // 오류: yell 접근할 수 없음, yell은 private 멤버임
    whisper() // 오류: whisper 접근할 수 없음, whisper는 protected 임
}
```

**자바에서는 같은 패키지안에서 protected는 멤버에 접근할 수 있지만, 코틀린에서는 그렇지 않다는 점에서 자바와 코틀린의 protected가 다르다. 코틀린의 protected는 오직 어떤 클래스나 그 클래스를 상속한 클래스 안에서만 접근 가능하다.**

### 내부 클래스와 중첩된 클래스: 기본적으로 중첩 클래스
클래스안에 다른 클래스를 선언하면 도우미 클래스를 캡슐화하거나 코드 정의를 그 코드를 **사용하는 곳 가까이 두고 싶을 때 유용하다.** 자바와의 차이는 코틀린의 중첩 클래스는 명시적으로 요청하지 않으면 바깥쪽 클래스 인스턴스에 대한 접근 권한이 없다는 점이다.



```kotlin
interface State : Serializable

interface View {
    fun getCurrentState(): State
    fun restoreState(state: State)
}
```
Button 클래스의 상태를 저장하는 클래스는 Button 클래스 내부에 선언하면 편하다. 자바에서 그런 선언을 하려면 아래 코드와 같다

```java
pulbic class Buttoin implements View {

    @Override
    public State getCueentState() {
        return new ButtonState();
    }
    
    @Override
    public void restoreState(State state) {...}

    public class ButtonState implements {...};

}
```

해당 코드는
```
java.io.NotSerializeableException:Button 
```
오류가 발생한다. **자바에서는 다른 클래스 안에 정의한 클래스는 자동으로 Inner Class가 된다. 이 예제는 ButtonState 클래스는 바깥쪽 Button 클래스에 대한 참조를 묵시적으로 포함한다. 그 참조로 인해 ButtonState를 직렬화할 수 없다. Button을 직렬화할 수 없음으로 버튼에 대한 참조 ButtonState의 직렬화를 방해한다.**

이 문제를 해결하려면 ButtonState를 static으로 선언해야 한다. **자바에서 중첩 클래스를 static으로 선언하면 그 클래스를 둘러싼 바깥쪽 클래스에 대한 묵시적인 참조가 사라진다.**

코틀린에서 중첩된 클래스가 기본적으로 동작하는 방식은 지금 설명한 것과 정반대다.

```kotlin
class Button : View {

    override fun getCurrentState(): State {}

    override fun restoreState(state: State) {}

    class ButtonState : State {} // 이 클래스는 자바 중첩(static class) 클래스와 대응한다

}
```
**코틀린 중첩 클래스에 아무런 변경자가 붙지 않으면 자바 static 중첩 클래스와 같다. 이를 내부 클래스로 변경해서 바깥쪽 클래스에 대한 참조를 포함하게 만들고싶다면 inner 변경자를 붙여야한다.**

클래스 B 안에 정의된 클래스 A              | 자바에서는          | 코틀린에서는
--------------------------------|----------------|--------------
중첩 클래스(바깥쪽 클래스에 대한 참조를 지정하지 않음) | static class A | class A
내부 클래스(바깥쪽 클래스에 대한 참조를 지정함)     | class A        | ineer class A

```kotlin
class Outer {
    inner class Inner {
        fun getOuterReference(): Outer = this@Outer
    }
}
```
**코틀린에서 바깥쪽 클래스의 인스턴스를 가리키는 참조를 표기하는 방법도 자바와는 다르다. 내 부클래스 Inner 안에서 바깥쪽 클래스 Outer의 참조를 접근하려면 `this@Outer`라고 써야 한다.**

### 봉인된 클래스 계층 정의 시 계층 확장 제한

```kotlin
interface Expr
class Num(val value: Int) : Expr
class Sum(val left: Expr, val right: Expr) : Expr

fun eval(e: Expr): Int =
    when (e) {
        is Num -> e.value
        is Sum -> eval(e.left) + eval(e.right)
        else -> throw IllegalArgumentException("")
    }
```
코틀린 컴파일러는 when을 사용해서 Expr 타입의 값을 검사할 때는 **꼭 디폴트 분기인 else 분기를 덧붙이게 강제한다**. else 분기는 반환할 만한 의미 있는 값이 없을때 예외를 던진다. **이는 안정성있는 코드이지만 항상 디폴트 분기를 추가해주는게 편리하지는 않다. 이러한 경우 sealed 클래스를 활용할 수 있다.**

```kotlin
sealed class Expr { // 기반 클래스를 sealed으로 봉인한다.
    class Num(val value: Int) : Expr() // 기반 클래스의 모든 하위 크래스를 중첩 클래스로 나열한다.
    class Sum(val left: Expr, val right: Expr) : Expr()
}


fun eval(e: Expr): Int =
    when (e) {
        is Expr.Num -> e.value // `when` 식이 모든 하위 클래스를 검사하므로 별도의 `else` 분기가 필요 없다.
        is Expr.Sum -> eval(e.left) + eval(e.right)
    }
```

**when 식에서 sealed 클래스의 모든 하위 클래스를 처리한다면 디폴트 분기가 필요 없다.** sealed class는 자동적으로 open이다.

## 뻔하지 않은 생성자와 프로퍼티를 갖는 클래스 선언

**코틀린은 주(primary) 생성자(보통 주 생성자는 클래스를 초기화할 때 주로 사용하는 간략한 생성자로, 클래스 본문 밖에서 정의한다.) 부 생성자(클래스 본문안에 정의한다)를 구분한다. 또 코틀린에서는 초기화 블록을 통해 초기화 로직을 추가할 수 있다.**

### 클래스 초기화: 주 생성자와 초기화 블록

```kotlin
class User constructor(_nickname: String) { // 주 생성자
    val nickname: String

    init { // 초기화 불록
        nickname = _nickname
    }
}
```
클래스 이름 뒤에 오는 괄호로 둘러싸인 코드를 주 생성자 라고 부른다. 주 생성자는 생성자 파라미터를 지정하고 그 생성자 파라미터에 의해 초기화되는 프로퍼티를 정의하는 두 가지 목적에 쓰인다.

constructor 키워드는 주 생성자나 부 생성자 정의를 시작할 때 사용한다. init 키워드는 초기화 블록을 시작한다. 초기화 블록에는 클래스의 객체가 만들어질때(인스턴스화할 때) 실행될 코드가 들어간다. **주 생성자는 제한적이기 때문에 별도의 코드를 포함할 수 없기 때문에  초기화 블록이 필요하다.**

```kotlin
class USer(_nickname: String) { // 파라미터가 하나뿐인 주 생성자
    val nickname = _nickname // 파라미터를 주 생성자의 파라미터로 초기화한다.
}
```

이러한 경우 constructor를 선택해도 된다.

```kotlin
class User (val nickname: String) // val은 이 파라미터에 상응하는 프로퍼티가 생성된다는 뜻이다.

class User (val nickname: String, val isSubscribed: Bollean = true) // isSubscribed 파라미터에 디폴트 값제공

>>> var yun = User("Yun")
>>> println(yun.isSubscribed) // true
>>> var wan = User("wan", false) // 모든 인자를 파라미터 선언 순서대로 지정할 수도 있다.
>>> var wan = User("wan", isSubscribed = false) // 생성자 인자 중 일부에 대해 이름을 지정할 수 있다.
```

기반 클래스를 초기화하려면 기반 클래스 이름 뒤에 괄호를 치고 생성자 인자를 넘긴다.

```kotlin
open class User (val nickname: String) {...}
class TwitterUser(nickname: String): User(nicname) {...}
```
클래스를 정의할 때 별도 생성자를 정의하지 않으면 컴파일러가 자동으로 아무 일도 하지 않은 인자 없는 디폴트 생성자를 만들어 준다. Button의 생성자는 아무런 인자도 받지 않지만, Buttoin 클래스를 상속한 하위 클래스는 반드시 Buttoin 클래스의 생성자를 호출해야 한다.

```kotlin
class RadioButtoin: Buttoin()
```
이 규칙으로 인해서 기반 클래스의 이름 뒤에는 꼭 빈 괄호가 들어간다. 반면 인터페이스는 생성자가 없기 때문에 어떤 클래스가 인터페이스를 구현하는 경우 그 클래스의 상위 클래스 목록에 있는 인터페이스 이름 뒤에는 아무 괄호도 없다.

어떤 클래스를 클래스 외부에서 인스턴스화하지 못하게 막고 싶다면 모든 생성자를 private으로 만들면 된다.

```kotlin
class Secretive private constructor() {...} // 이 클래스의 유일한 주 생성자는 비공개다.
```
Secretive 클래스 안에서 주 생성자 밖에 없고 그 주 생성자는 비공개이므로 외부에서 Secretive를 인스턴스화할 수 없다.

### 게터와 세터에서 뒷받침하는 필드에 접근

```kotlin
class User(val name: String) {
    var address: String = "unspecified"
        set(value: String) {
            println(
                """
                Address was changed for $name:
                "$field" -> "$value".""".trimIndent()
            )
            field = value
        }
}

>> Address was changed for yun:
"unspecified" -> "신림역".
>> Address was changed for yun:
"신림역" -> "낙성대".
```
접근자의 본문에서 field라는 특별한 식별자를 통해 뒷받침하는 필드에 접근할 수있다. 게터에서는 값을 일을 수만 있고, 세터에서는 field 값을 읽거나 쓸수 있다.

### 접근자의 가시성 변경
```kotlin
class LengthCounter {
    var counter: Int = 0
        private set
    
    fun addWord(word: String) {
        counter += word.length
    }
}
```
접근자의 가지성은 기본적으로 프로퍼티의 가시성과 같다. 하지만 **원한다면 get, set 앞에 가시성 변경자를 추가해서 접근자의 가기성을 변경할 수 있다.**

## 컴파일러가 생성한 메서드: 데이터 클래스와 클래스 위임
자바에서는 클래스가 equals, hashCode, toString 등의 메소드를 구현해야 한다. **코틀린에서는 메서드를 기계적으로 생성하는 작업을 보이지 않은 곳에서 해준다. 따라서 코드를 깔끔하게 유지할 수 있다.**

### 모든 클래스가 정의해야 하는 메서드
자바와 마찬가지로 코틀린 클래스에서도 toString, equals, hashCode 등을 오버라이드 할 수 있다.

```kotlin
class Client(val name: String, val postalCode: Int) {

    override fun toString(): String {
        return "Client(name='$name', postalCode=$postalCode)"
    }

    override fun equals(other: Any?): Boolean {
        if (this === other) return true
        if (other !is Client) return false

        if (name != other.name) return false
        if (postalCode != other.postalCode) return false

        return true
    }

    override fun hashCode(): Int {
        var result = name.hashCode()
        result = 31 * result + postalCode
        return result
    }
}
```
#### 문자열 표현: toString()
자바의 ToString과 동일하다.

#### 객체의 동등성: equals()
> **동등성 연산애 ==를 사용함**
> 
> 자바에서 == 원시 타입과 참조 타입을 비교할 때 사용한다. 코틀린에서 == 연산자가 두 객체를 비교하는 기본적인 방법이다 ==는 내부적으로 equals를 호출해서 객체를 비교한다. 따라서 클래스가 equals를 오버라이드하면 ==를 통해서 안전하게 클래스의 인스턴스륿 비교할 수 있다. 참조 비교를 위해서는 === 연산자를 사용할 수 있다. === 연산자는 자바에서 객체의 참조를비교할 때 사용하는 == 연산자와 같다.

#### 해시 컨테이너: hashCode()

HashSet은 원소를 비교할 때 비용을 줄이기 위해 먼저 객체의 해시 코드를 비교하고 해시코드가 같은 경우에만 실제 값을 비교한다. **즉 원소 객체들이 해시 코드에 대한 규칙을 지키지 않은 경우 HashSet은 제대로 작동할 수 없다. 그러기 위해서 hashCode를 구련해야 한다.**

## 데이터 클래스: 모든클래스가 정의해아 하는 메서드 자동 생성

코틀린에서는 data라는 변경자를 클래스 앞에 붙이면 필요한 메서드를 컴파일러가 자동으로 만들어 준다. data 변경자가 붙은 클래스를 데이터 클래스라고 한다.


```kotlin
data class Client(val name: String, val postalCode: Int)
```
data class는 아래 메서드를 자동으로 만들어준다.
* 이스턴스 간 비교를 위한 equals
* HashMap과 같은 해시 기반 컨테이너에서 키로 사용할 수 있는 hashCode
* 클래스의 각 필드를 선언 순서대로 표시하는 문자열 표현을 만들어주는 toString

## 클래스 위임: by 키워드 사용
...

## object 키워드: 클래스 선언과 인스턴스 생성
코틀린에서 object 클래스를 정의 하면서 동시에 인스턴스를 생성한다는 공통점이 있다. 

* 객체 선언은 싱글턴을 정의하는 방법 중 하나다.
* 동반 객체는 인스턴스 메서드는 아니지만 **어떤 클래스와 관련 있는 메서드와 팩토리 메서드를 담을 때 쓰인다.** 동반 객체 메서드에 접근할 때는 동반 객체가 포함된 클래스의 이름은 사용할 수 있다.
* 객체 식은 자바의 **무명 내부 클래스** 대신에 쓰인다.

### 객체 선언: 싱글턴 쉡게 만들기
코틀린은 객체 선언 기능을 통해 싱글턴을 언어에서 기본 지원한다. 객체 선언은 클래스 선언과 그 클래스에 대한 **단일 인스턴스의 선언을 합친 것이다.**

```kotlin
object Payroll {
    val allEmployees = arrayListOf<Person>()
    fun calculateSalary() {
        for (person in allEmployees) { }
    }
}
```
객체 선언은 클래스를 정의하고 그 클래스의 인스턴스를 만들어서 변수에 저장하는 모든 작업을 단 한문장으로 처리한다. 일반 클래스 인스턴스와 달리 싱글턴 객체는 객체 선언문이 있는 위치에서 생성자 호출 없이 즉시 만들어진다. 따라서 객체 선언에는 생성자 정의가 필요 없다.

### 동반 객체: 팩토리 메서드와 정적 멤버가 들어갈 장소
코틀린 클래스 안에는 정적 멤버가 없다. 코틀린 언어는 자바 static 키워드를 지원하지 않는다. **그 대신 코틀린에서는 패키지 수준의 최상 함수(자바의 정적 메서드 역할을 거의 대신 할 수 있다,)와 객체 선언(자바의 정적 메서드 역할 중 코틀린 최상위 함수가 대신할 수 없는 역할이나 정적 필드를 대신할 수 있다.) 대부분의 경우 최상위 함수를 활용 하는 편을 더 권장한다.** 


클래스 안에 정의된 객체 중 하나에 **companion이라는 특별한 표시를 붙이면 그 클래스의 동반 객체러 만들 수 있다.** 동반 객체의 프로퍼티나 메서드에 접근하려면 그 동반 객체가 정의된 클래스 이름을 사용한다.

```kotlin
class A {
    companion object {
        fun bar() {
            print("bar...")
        }
    }

    fun foo() {
        print("foo..")
    }
}

fun asd(){
    A.bar() // 접근 가능
    A.foo() // 접근 불가능
}
```

### 동반 객체를 일반 객체 처럼 사용

```kotlin
class User private constructor(val nickname: String) {
    companion object {
        fun newSubscribingUser(email: String) = User(email.substringBefore('@'))
        fun newFacebookUser(accountId: Int) = User(getFacebookName(accountId))
    }
}

fun newInstance() {
    User.newSubscribingUser("asd@asd.com")
    User.newFacebookUser(1)
}
```
간결하게 자바의 static factory method 방식을 구현 할 수 있다.

#### 동반 객체에서 인터페이스 구현 
다른 객체 선언과 마찬가지로 동반 객체도 인터페이스를 구현 할 수 있다.

```kotlin
interface JsonFactory<T> {
    fun fromJson(jsonText: String): T
}

class Person(val name: String) {
    companion object : JsonFactory<a4.Person> {
        override fun fromJson(jsonText: String): a4.Person {
            return a4.Person("....")
        }
    }
}
```

#### 동반 객체 확장
Person 클래스는 핵심 비지니스 로직 모듈이다. 하지만 그 비지니스 모듈이 특정 데이터 타입에 의존하기를 원치 않는다. **따라서 역직렬화 함수를 비지니스 모듈이 아니라 클라인트/서버 통신을 담당하는 모듈 안에 포함시키고 싶다. 이때 확장 함수를 사용하면 아래와 같이 구조를 가질 수 있다.**

‼️ 자바에서 많이 고민 했던 부분이다. 좋은 해결인거 같다.

```kotlin
// 비지니스 로직 모듈 : 해당 객체
class Person(val firstName: String, val lastName: String) {
    companion object {}
}

// 클라이언트, 서버 통신 모듈
fun Person.Companion.fromJson(json: String): a4.Person {
    return Person("firstName", "lastNameL`")
}

val person = Person.fromJson(json)
```
**마치 동반 객체 안에서 fromJson 함수를 정으힌 것처럼 함수를 호출할 수 있다.** 동반 객체에 대한 확장 함수를 작성할 수 있으려면 **원래 클래스에 동반 객체를 꼭 선언 해야한다.**


### 객체 식: 무명 내부 클래스를 다른 방식으로 작성
object 키워드를 싱글턴과 같은 객체를 정의하고 그 객체에 이름을 붙일 때만 사용하지 않는다. 무명 객체를 정의할 때도 object 키워드를 쓴다. 무명 객체는 자바의 무명 내부 클래스를 대신한다.

```kotlin
val listener = object : MouseAdapter() {
    override fun mouseClicked(e: MouseEvent?) {
        super.mouseClicked(e)
    }
    override fun mouseEntered(e: MouseEvent?) {
        super.mouseEntered(e)
    }
}
```

# 05 람다로 프로그래밍
**람다 식 또는 람다는 기본적으로 다른 함수에 넘길 수 있는 작은 코드 조각을 뜻한다.**

## 람다 식과 멤버 참조

### 람다 소개: 코드 블록을 함수 인자로 넘기기
이전 자바에서는 **무명 내부 클래스를 사용하면 코드를 함수에 넘기거나 변수에 저장할 수 있기는 하지만 상당히 번거롭다.** 코틀린에서 클래스를 선언하고 그 클래스의 인스턴스를 함수에 넘기는 대신 함수형 언어에서 함수를 직접 다른 함수에 전달할 수있다.

```java
// 자바
button.setOnclickListener(new OnClickListener() {
    @Override
    public void onClick(View view) {
        // 클릭 시 수행할 동작
    }
});
```
`new OnClickListener(){...}` 무명 내부 클래스를 함수를 넘기는 방식이다.

### 람다와 컬렉션
```kotlin
val person = listOf(Person("yun", 29), Person("wan", 28))

fun main() {
    print(person.maxBy { it.age }) // 람다를 사용해 컬렉션 검색하기
    println(person.maxBy(Person::age)) // 멤버 참조를 사용해 컬렉션 검색하기

}
```
maxBy는 가장 큰 원소를 찾기 위해 비교에 사용할 값을 돌려주는 함수를 인자로 받는다.

### 람다 식의 문법
**람다는 값처럼 여기저기 전달할 수 있는 동작의 모음이다. 람다를 따로 선언해서 변수에 저장할 수도 있다. 하지만 함수에 인자로 넘기면서 바로 람다를 정의하는 경우가 대부분이다.**

```
{x: Int, y: int -> x + y}

x: Int, y: in : 파라미터
x + y : 본문
```

#### 람다 파라미터 타입 제거하기
```kotlin
person.maxBy { p: Person -> p.age }
person.maxBy { p -> p.age}
```
로컬 변수처럼 컴파일러는 람다 파라미터의 타입도 추론할 수 있다. 따라서 파라미터 타입을 명시할 필요가 없다. `maxBy` 함수의 경우 파라미터의 타입은 항상 컬렉션 원소 타입과 같다.

### 현재 영역에 있는 변수에 접근
람다 함수 안에서 정의하면 함수의 파라미터뿐 아니라 람다 정의의 앞에서 선언된 로컬 변수까지 람다에서 모두 사용할 수 있다.

#### 함수 파라미터를 람다 안에서 사용하기
```kotlin
fun printMessageWithPrefix(message: Collection<String>, prefix: String) {
    message.forEach {
        print("$prefix $it")
    }
}
```
자바와 다르 점 중 중요한 한 가지는 **코틀린 람다 안에서 파이널 변수가 아닌 변수에 접근할 수 있단다는 점이다. 또한 람다 안에서 바깥의 변수를 변경해도 된다.**

```kotlin
fun printProblemCounts(responses: Collection<String>) {
    var clientErrors = 0 // 람다에서 사용할 변수를 정의한다.
    var serverErrors = 0 // 람다에서 사용할 변수를 정의한다.

    responses.forEach {
        if (it.startsWith("4")) {
            clientErrors++ // 람다 안에서 람다 밖의 변수를 변경한다.
        } else if (it.startsWith("5")) {
            serverErrors++ // 람다 안에서 람다 밖의 변수를 변경한다.
        }
    }

    println("$clientErrors client errors, $serverErrors server errors")
}
```
**코틀린에서는 자바와 달리 람다에서 람다 밖 함수 있는 파이널 아닌 변수에 접근할 수 있고, 그 변수를 변경할 수 있다.** 람다 안에서 사용하는 외부 변수를 **람다가 포획한 변수**라고 한다

파이널 변수를 포획한 경우에는 람다 코드를 변수 값과 함께 저장한다. 파이널 아닌 변수를 포획한 경구에는 변수를 특별한 래퍼로 감싸서 나중에 변경하거나 읽을 수 있게 한 다음 래퍼에 대한 참조를 람다 코드와 함께 저장한다.

##### 변경 가능한 변수 포획하기: 자세한 구현 방법
자바에서는 파이널 변수만 포획할 수 있다. 하지만 교모한 속임수를 통해 변경 가능한 변수를 포획할 수 있다. **그 속임수는 변경 가능한 변수를 저장하는 원소가 단 하나뿐인 배열을 선언하거나, 변경 가능한 변수를 필드로 하는 클래스를 선언하는 것이다.**

```kotlin
class Ref<T> (val value: T) // 변경 가능한 변수를 포획하는 방법을 보여주기 위한 클래스
>>> val count = Ref(0) 
>>> val inc = { counter.Value++ } // 공싱적으로 변경 불가능한 변수를 포홱했지만 그 변수가 가리키는 객체의 멤버 필드 값을 바꿀수 있다.
```

### 멤버 참조
**코드가 이미 함수로 선언된 경우 함수를 직접 넘길 수 있다.** 이때 이중 콜론 `(::)`을 사용한다.

```kotlin
val getAge = Person::age
```
`::`룰 사용하는 식을 멤버 참조 라고 부른다. **멤버 참조는 프로퍼티나 메서드를 단 하나만 호출하는 함수 값을 만들어준다. `::`는 클래스 이름과 여러분이 참조하려는 멤버(프로퍼티나 메서드) 이름 사이에 위치한다**

```
Person::age

Person -> 클래스
age -> 멤버
:: 으로 구분 
```

```kotlin
person.maxBy(Person::age)
person.maxBy( p -> p.age )
person.maxBy( it.age)
```
멤버 참조는 그 멤버를 호출하는 람다와 같은 타입이다.
