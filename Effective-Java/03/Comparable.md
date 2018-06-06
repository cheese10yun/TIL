# Comparable 구현을 고려하라
* CompareTo 메서드는 Object에 선언되지 않다.
* Comparable 인터페이스에 포함된 유일한 메서드이다.
* equals 메서드와 특성은 비슷하지만, 단순한 동치성 검사 이외에 순서 비교가 가능하며, 좀 더 일반적이다.



Stringd이 Comparable을 구현하고 있다는 사실을 이용하여 명령 인자들을 알파벳 순서로 정렬하는 동시에 중복을 제거하고 있다.
```java
@Test
public void set() {
    final String[] args = {"f", "f", "z", "a", "b", "e", "e", "h", "j", "k"};
    final Set<String> s = new TreeSet<>();
    Collections.addAll(s, args);
    System.out.println(s); //[a, b, e, f, h, j, k, z]
    // 중복제거, 순서 정렬
}
```
comparable을 구현한 클래스는 다양한 제네릭 알고리즘 및 Comparable 인터페이스를 이용하도록 작성된 컬렉셔 구현체와도 전부 연동 할 수있다.


## 규칙

* 첫 번쨰 항목은 객체 참조를 비교하는 방향을 뒤집어도 객체 간 대소 관계는 그대로 유지되어야 한다.
    * 즉 첫 번째 객체가 두 번째 객체보다 작다면, 드 번쨰 객체는 첫 번쨰 객체보다 큰 객체여야 한다.
    * 당연 한거다 1 < 2 true 면 , 2 > 1 true 이다

## example code
```java
public int compareTo(PhoneNumber pn){
// 지역번호 비교
if (areaCode < pn.areaCode) return -1;
if (areaCode > pn.areaCode) return 1;

//지역번호 같으니 국번 비교
if(prefix < pn.prefix) return -1;
if(prefix > pn.prefix) return 1;

//지역번호 국번 같음으로 회선 비교
if(lineNumber < pn.lineNumber) return -1;
if(lineNumber > pn.lineNumber) return 1;

return 0; // 모드 일치하니
}
```
 정상 작동하는 메서드이나 개선의 여지는 있다. compareTo 메서드는 일반 규약이 반환값의 부호만 명시할 뿐 그 크기에 대해서는 언급하고 있지 않음을 상기하자

## refactoring example code
 ```java
 public int compareTo(PhoneNumber pn){
// 지역번호 비교
int areaCodeDiff = areaCode - pn.areaCdoe;
if (areaCodeDiff != 0) return areaCodeDiff;


//지역번호 같으니 국번 비교
int prefixDiff = prefix - pn.prefix;
if (prefixDiff != 0) return prefixDiff;


return lineNumber - pn.lineNumber
}
 ```
