## Hast Table 이란 ?
* 검색 하고자하는 key를 입력받아서 hash 함수를 통해서 받은 hash code를 배열의 Index로 변경해서 Value를 찾는 것

## 장점
![](/assets/hast-table-1.png)

* 검색 속도가 빠르다.
* hash 함수를 통해서 생성한 hash code는 정수이다.
* 배열 공간을 미리 고정된 크기로 미리 만들고 hash code를 배열의 갯수로 나머지 연산을 해서 배열에 저장
* hash code가 배열의 index 이다

## Collision
![](/assets/hashtable-Collision.png)

* hash code를 최대한 중복되지 않게 골고루 분배하는 것이 중요
* 서로 다른 key 값이더라도 중복 hash code를 생성할 수 도 있음
* Collision
  * different keys -> Same Code : 다른 key값이지만 동일한 hash code를 갖는 경우
  * different code -> Same Index : 다른 code 지만 동일한 Index를 갖는 경우
  * Collision이 많이 발생하면 O(1) -> O(N) 성능이 변할 수도 있음
  * key 값은 무한한 값인 반면에 hash code는 정수의 갯수만큼 제공되므로 중복되는 키를 피할 수는 없다.
  


## Hash Code 생성
![](/assets/hash-table-hash-code.png)

* 간단한 예제, 실제로 이런 코드로 hash code를 생성하면 Collision 자주 발생하게 된다.

![](/assets/hast-table-convent.png)

* hashCode % array-size
* hashCode가 골고루 분배되지 않아 좋은 알고리즘은 아니다.

## Sample Code

```java
public class HastTable {

    private final LinkedList<Node>[] data;

    public HastTable(int size) {
        this.data = new LinkedList[size];
    }

    public int getHastCode(String key) {
        int hashCode = 0;
        for (char c : key.toCharArray()) {
            hashCode += c;
        }
        return hashCode;
    }

    public int convertToIndex(int hashCode) {
        return hashCode % data.length;
    }

    public Node searchKey(LinkedList<Node> list, String key) {
        if (list == null) return null;

        // 해당 인덱스의 링크드 리스트를 전체 순회하면서 키값을 찾는다.
        for (Node node : list) {
            if (node.key.equals(key)) {
                return node;
            }
        }
        return null;
    }

    public void put(String key, String vale) {
        final int hashCode = getHastCode(key);
        final int index = convertToIndex(hashCode);
        LinkedList<Node> list = data[index];

        // list가 없다면 링크드 리스트<Node>를 만들고 배열 인덱스에 링크드 리스트를 넣는다
        if (list == null) {
            list = new LinkedList<>();
            data[index] = list;
        }

        final Node node = searchKey(list, key);
        if (node == null) {
            //노드를 찾을 수 없으면 마지막 노드에 추가한다
            list.addLast(new Node(key, vale));
        } else {
            //노드가 있다면 새롭게 value 값을 업데이트 한다
            node.setValue(vale);
        }
    }

    public String get(String key) {
        final int hashCode = getHastCode(key);
        final int index = convertToIndex(hashCode);
        final LinkedList<Node> list = data[index];
        final Node node = searchKey(list, key);

        return node == null ? "Not found" : node.getValue();
    }


    class Node {

        private String key;
        private String value;

        public Node(String key, String value) {
            this.key = key;
            this.value = value;
        }

        public String getKey() {
            return key;
        }

        public void setKey(String key) {
            this.key = key;
        }

        public String getValue() {
            return value;
        }

        public void setValue(String value) {
            this.value = value;
        }
    }
}
```

## 출처
* [[자료구조 알고리즘] 해쉬테이블(Hash Table)에 대해 알아보고 구현하기](https://www.youtube.com/watch?v=Vi0hauJemxA&feature=youtu.be)