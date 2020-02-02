# 기타

## ResourceLoader 
```kotlin
abstract class SpringBootTestSupport {
    @Autowired
    protected lateinit var resourceLoader: ResourceLoader

    protected val CLASS_PATH = "classpath:"

    protected fun <T> read(path: String, typeReference: TypeReference<List<T>>): List<T> {
        val json = resourceLoader.getResource("$CLASS_PATH$path").inputStream
        return objectMapper.readValue(json, typeReference)
    }
}
```
* resourceLoader를 이용해서 resource를 쉽게 읽어 올 수 있음
* `classpath:`는 `/src/test/resources/`을 기본 path를 바라 봄
* `JSON`을 쉽게 읽어 올 수 있음


## ObjectMapper
```kotlin
protected fun <T> read(path: String, typeReference: TypeReference<List<T>>): List<T> {
    val json = resourceLoader.getResource("$CLASS_PATH$path").inputStream
    return objectMapper.readValue(json, typeReference)
}

protected fun <T> readPage(path: String, typeReference: TypeReference<PageResponse<T>>): PageResponse<T> {
    val json = resourceLoader.getResource("$CLASS_PATH$path").inputStream
    return objectMapper.readValue(json, typeReference)
}
```
* objectMapper 단순 객체타입이 아닌 컬렉션 타입 및 제네릭 타입을 역직렬화가 필요한 경우 `TypeReference`을 사용하면 됨