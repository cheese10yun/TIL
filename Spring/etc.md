# 기타
그냥 짜잘한 것들 정리하자...

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

## RestTemplate

```kotlin
@Service
class PaymentRestService(
    private val paymentRestTemplate: RestTemplate
) {

    fun requestPayment(amount: BigDecimal, page: Int, size: Int): PageResponse<Payment> {
        val url = UriComponentsBuilder.fromUri(URI.create("http://localhost:8080/payment"))
            .queryParam("amount", amount)
            .queryParam("page", page)
            .queryParam("size", size)
            .build()

        val request = RequestEntity<Any>(HttpMethod.GET, url.toUri())
        val respType = object: ParameterizedTypeReference<PageResponse<Payment>>(){}

        return paymentRestTemplate.exchange(request, respType).body!!
    }
}
```
* `RestTemplate`를 기반으로 Generic type, Collection Type 기반으로 데이터를 받아야 하는 경우 `ParameterizedTypeReference`을 활용해서 받을 수 있다.
* 아마 `exchange`으로 활용해야 한다. `getForEntity()`, `getForObject()` 으로는 `ParameterizedTypeReference`을 이용 못하는 거같음..