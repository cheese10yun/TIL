## Lamda

```java
final List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);

        final String result = numbers.stream()
                .map(String::valueOf)
                .collect(joining(" : "));
```

* `map`
    * key, value 가아니라 다른 타입으로 매핑한다는 의미
* `String::valueOf`
    * String.valueOf - > Integer to String
