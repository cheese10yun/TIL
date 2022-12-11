# Collection 가공

## groupBy

```kotlin
data class Member(
    val name: String,
    val status: MemberStatus
)

enum class MemberStatus(description: String) {
    NORMAL("정산"),
    VAN("정지")
}

class Collection {
    @Test
    fun groupBy() {
        val members = (1..50).map {
            Member(
                name = "name${it}",
                status = when {
                    (0..1).random() == 0 -> MemberStatus.NORMAL
                    else -> MemberStatus.VAN
                }
            )
        }

        val groupBy = members.groupBy { it.status }
        val vanMembers = groupBy[MemberStatus.VAN] // (1)
        val normalMembers = groupBy[MemberStatus.NORMAL] // (2)
        // (3)
        val groupBy1 = members.groupBy(
            { it.status },
            { it.name }
        )
    }
}

```

![](https://raw.githubusercontent.com/cheese10yun/blog-sample/master/effective-kotlin/effective-kotlin/images/kotlin-1.png)

컬렉션의 특정 값으로 그룹화를 진행이 가능하다. 그룹화 한 값은 LinkedHashMap 컬렉션으로 리턴되며 (1),(2)처럼 Key 값으로 리턴할 수 있다.

![](https://raw.githubusercontent.com/cheese10yun/blog-sample/master/effective-kotlin/effective-kotlin/images/kotlin-2.png)
만약 리턴되는 객체를 컬렉션 요소의 객체가 아닌 객체를 리턴 받고 싶다면 (3)처럼 가능하다. 해당 객체는 Member 객체가 아닌 name 필드만 추출한 코드이다.

## groupingBy

```kotlin

data class Order(
    val orderNumber: String,
    val status: OrderStatus,
    val price: Int
)

enum class OrderStatus(description: String) {
    COMPLETE_ORDER("주문 완료"),
    COMPLETE_PAYMENT("결제 완료"),
}

class Collection {
    @Test
    fun groupingBy() {
        val orders = (1..10).map {
            Order(
                orderNumber = "name${it}",
                status = when {
                    (0..1).random() == 0 -> OrderStatus.COMPLETE_ORDER
                    else -> OrderStatus.COMPLETE_PAYMENT
                },
                price = (100..15000).random()
            )
        }

        // (1)
        val groupingBy = orders
            .groupingBy { it.status }

        // (2)
        val aggregate = groupingBy
            .aggregate { key, accumulator: OrderPrice?, element, first ->
                when {
                    first -> OrderPrice(orderStatus = key, price = element.price)
                    else -> OrderPrice(orderStatus = key, price = accumulator!!.price + element.price)
                }
            }

        val totalPrice1 = aggregate[OrderStatus.COMPLETE_ORDER]
        val totalPrice2 = aggregate[OrderStatus.COMPLETE_PAYMENT]

        // (3)
        val eachCount = groupingBy.eachCount()
        val count1 = eachCount[OrderStatus.COMPLETE_ORDER]
        val count2 = eachCount[OrderStatus.COMPLETE_PAYMENT]
    }
}
```

groupingBy는 컬렉션에 대해서 그룹화하여 이후 다양한 연산을 편리한 제공할 수 있는 Grouping 객체로 리턴해줍니다.

![](https://raw.githubusercontent.com/cheese10yun/blog-sample/master/effective-kotlin/effective-kotlin/images/kotlin-3.png)

(1) groupingBy으로 Grouping 객체로 응답받고 해당 확장함 수로 다양한 연산 작업이 가능합니다. (2) aggregate으로 groupingBy된 값 기반으로 가격에 대한 sum 작업, (3) eachCount으로 groupingBy된 카운트를 조회 가능합니다.

## chunked

```kotlin

class Collection {
    @Test
    fun chunked() {
        val orders = (1..100).map {
            Order(
                orderNumber = "name${it}",
                status = when {
                    (0..1).random() == 0 -> OrderStatus.COMPLETE_ORDER
                    else -> OrderStatus.COMPLETE_PAYMENT
                },
                price = (100..15000).random()
            )
        }

        val chunked = orders.chunked(10)
    }
}
```

chunked는 컬렉션 객체를 넘겨받은 인자 크기만큼 컬렉션을 나눕니다.

![](https://raw.githubusercontent.com/cheese10yun/blog-sample/master/effective-kotlin/effective-kotlin/images/kotlin-3.png)

100개의 orders 객체를 10개로 나누면 10개 컬렉션이 10개가 됩니다. 말 그대로 청크를 나누는 컬렉션 객체입니다. 처리해야 할 데이터가 너무 크다면 적절한 청크 처리하는 경우 유용합니다.

## flatMap

```kotlin
class Collection {

    data class OrderNumbers(
        val orderStatus: OrderStatus,
        val orderNumbers: Set<String>
    )

    enum class OrderStatus(description: String) {
        COMPLETE_ORDER("주문 완료"),
        COMPLETE_PAYMENT("결제 완료"),
    }

    @Test
    fun flatMap() {
        val orderNumbers = listOf(
            OrderNumbers(
                orderStatus = OrderStatus.COMPLETE_ORDER,
                orderNumbers = setOf("order-number-1", "order-number-2", " order-number-3", "order-number-3"),
            ),
            OrderNumbers(
                orderStatus = OrderStatus.COMPLETE_ORDER,
                orderNumbers = setOf("order-number-4", "order-number-5", " order-number-6", "order-number-7"),
            ),
            OrderNumbers(
                orderStatus = OrderStatus.COMPLETE_PAYMENT,
                orderNumbers = setOf("order-number-8", "order-number-9", " order-number-10", "order-number-11"),
            ),
            OrderNumbers(
                orderStatus = OrderStatus.COMPLETE_PAYMENT,
                orderNumbers = setOf("order-number-11", "order-number-12", " order-number-13", "order-number-14"),
            )
        )

        val flatMap = orderNumbers.flatMap { it.orderNumbers }
    }
}
```

flatMap은 여러 컬렉션을 합쳐서 하나의 컬렉션으로 합쳐줍니다. 위처럼 여러 컬렉션 객체 안의 컬렉션 객체가 있는 경우 하나의 컬렉션으로 flatMap을 통해서 합칠 수 있습니다.

![](https://raw.githubusercontent.com/cheese10yun/blog-sample/master/effective-kotlin/effective-kotlin/images/kotlin-5.png)

orderNumbers로 각 객체에 있던 값들을 한 컬렉션으로 합쳐진 것을 확인할 수 있습니다.