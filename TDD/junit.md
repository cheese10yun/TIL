# Junit


## anyOf
```java
assertThat(isRunning, anyOf(
  equalTo(true),
  equalTo(false)
))
```
* `equalTo` 중에 하나 ~

## is number

## equalTo
```java
assertThat(createUser, equalTo(findUser));
```

* 두 객체의 값이 동일한지 비교
