<p align="center">
  <img src="http://image.kyobobook.co.kr/images/book/xlarge/072/x9791163030072.jpg">
</p>

[Do it! 자료구조와 함께 배우는 알고리즘 입문: 자바 편 ](http://www.kyobobook.co.kr/product/detailViewKor.laf?ejkGb=KOR&barcode=9791163030072#N)

# 목차
<!-- TOC -->

- [목차](#목차)
- [03 검색](#03-검색)
    - [선형 검색](#선형-검색)
        - [보초법](#보초법)
    - [이진 검색](#이진-검색)
    - [복잡도](#복잡도)

<!-- /TOC -->

# 03 검색

* 선형 검색 : 무작위로 늘어놓은 데이터 모임에서 검색을 수행합니다.
* 이진 검색 : 일정한 규칙으로 늘어놓은 데이터 모임에서 아주 빠른 검색을 수행합니다.
* 해시 기법 : 추가, 삭제가 자주 일어나는 데이터 모임에서 아주 빠른 검색을 수행합니다.
    * 체인법 : 같은 해기 같의 데이터를 선형 리스트로 연결하는 방법
    * 오픈 주소법 : 데이터를 위한 해시 값이 충돌할 때 재해시하는 방법

## 선형 검색
요소가 직선 모양으로 늘어난 배열에서 검색을 원하는 키 값을 갖는 요소를 만날 때까지 맨 앞부터 순서대로 요소를 검색하면 되는데, 이것이 선형 검색 또는 순차 검색이라는 알고리즘입니다.

### 보초법
선형 검색은 반복할 때마다 당므의 종료 조건 (1)과 (2)를 모두 판단합니다. **종료 조건을 검사하는 비용은 결코 무시할 수 없습니다.**

```
종료 조건 (1) 검색할 값을 반경하지 못하고 배열의 끝을 지나는 경우
종료 조건 (2) 검색할 값과 같은 요소를 발견할 경우
```
**이 비용을 반으로 줄이는 방법이 보초법입니다.** 검색하기 전에 검색 하고자하는 키 값을 맨 끝요소에 저장합니다. 이때 저장하는 값을 보초라고 합니다.
그러면 원하는 값이 원래의 데이터에 존재하지 않아도 보초 값까지 검색하면 죵료 조건 (2)가 성립합니다. 이렇게 하면 원하는 키 값을 찾지 못했을 때 값을 판단하는 종료 조건 (1)이 없어저도됩니다. **보초는 반복문에서 종료 판단횟루를 2회에서 1회로 줄이는 역할을 합니다.**

```java
public class SeqSearch {

    static int seqSearch(int[] a, int n, int key) {

        int i = 0;
        a[n] = key; // 보초키 추가

        while (a[i] != key) i++;

        return i == n ? -1 : i;
    }

    public static void main(String[] args) {
        Scanner stdIn = new Scanner(System.in);

        System.out.println("요솟수 : ");
        int num = stdIn.nextInt();
        int[] x = new int[num + 1]; // +1  보초키 추가

        for (int i = 0; i < num; i++) {
            System.out.println("x[" + i + "]: ");
            x[i] = stdIn.nextInt();
        }

        System.out.println("검색할 값: ");
        int key = stdIn.nextInt();
        int idx = seqSearch(x, num, key);

        if (idx == -1)
            System.out.println("없음");
        else
            System.out.println(idx);
    }
}
```

## 이진 검색
이 알고리즘을 적용하는 전제 조건은 데이터가 키 값으로 이미 정렬되어 있다는 것입니다. 이진 검색은 선형 검색보다 좀 빠르게 검색 할수 있다는 장점이 있습니다.

이진 검색은 요소가 오름차순 또는 내림차순으로 정렬된 배열에서 검색하는 알고리즘입니다. 
검색 과정은 먼저 배열의 중앙에 위치한 요소 부터 검색을 시작합니다. 검색하려는 값이 중앙 요소 보다 큰값일 경우 중앙 기준으로 오른쪽으로 범위를 좁힐 수 있습니다. 그런 다음 다음 검색 범우의 중앙에 우치한 검색 요소를 기준으로 방금전 작업을 반복합니다. 

```
pl : index[first]
pc : index[중앙]
pr : index[last]
```

```java
static int binSearch(int[] a, int n, int key) {
    int pl = 0; // 검색 범위 첫 인덱스
    int pr = n - 1; // 검색 범위 끝 인덱스
    
    do {
        int pc = (pl + pr) / 2; // 중앙 인덱스
        if (a[pc] == key)
            return pc; // 검색 완료
        else if (a[pc] < key)
            pl = pc + 1; // 검색 범위를 뒤쪽 절반으로 좁힙
        else
            pr = pc - 1; // 검색 범위를 앞쪽 절반으로 좁힙
    } while (pl <= pr);

    return -1;
}
```

## 복잡도
* 시간 복잡도 : 실행에 필요한 시간을 평가하는 것
* 공간 복잡도 : 기억 영역과 파일 공간이 얼마나 필요한가를 평가한 것

단계 | 실행횟수 | 복잡도
---|------|------
1  | 1    | O(1)
2  | n/2  | O(n)
3  | n/2  | O(n)
4  | 1    | O(1)
5  | n/2  | O(n)
6  | 1    | O(1)


