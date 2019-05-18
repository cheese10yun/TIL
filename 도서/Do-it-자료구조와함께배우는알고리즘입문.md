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
- [04 스택과 큐](#04-스택과-큐)
    - [스택](#스택)
        - [스택 만들기](#스택-만들기)
            - [스택 본체용 배열](#스택-본체용-배열)
            - [스택의 용량 : max](#스택의-용량--max)
            - [스택 포인터 : ptr](#스택-포인터--ptr)
            - [생성자 intStack](#생성자-intstack)
            - [푸쉬 메서드 push](#푸쉬-메서드-push)
            - [팝 메서드 pop](#팝-메서드-pop)
            - [피크 메서드 peek](#피크-메서드-peek)
            - [검색 메서드 indexOf](#검색-메서드-indexof)
            - [스택의 모든 요소를 삭제 하는 메서드 clear](#스택의-모든-요소를-삭제-하는-메서드-clear)
            - [용량을 확인하는 메서드 capacity](#용량을-확인하는-메서드-capacity)
            - [데이터 수를 확인하는 메서드 size](#데이터-수를-확인하는-메서드-size)
            - [스택이 비어있는지 검사하는 메서드 isEmpty](#스택이-비어있는지-검사하는-메서드-isempty)
            - [스택이 가득 차 있는지 검사하는 메서드 isFull](#스택이-가득-차-있는지-검사하는-메서드-isfull)
            - [스택 안에 있는 모든 데이터를 표시하는 메서드 dump](#스택-안에-있는-모든-데이터를-표시하는-메서드-dump)
    - [큐](#큐)
        - [배열로 큐 만들기](#배열로-큐-만들기)
        - [링 버퍼로 큐 만들기](#링-버퍼로-큐-만들기)
            - [인큐 메서드 enque](#인큐-메서드-enque)
            - [디큐 메서드 deque](#디큐-메서드-deque)
            - [검색 메서드 indexOf](#검색-메서드-indexof-1)
            - [모든 데이터를 출력하는 메서드 dump](#모든-데이터를-출력하는-메서드-dump)
- [07 집합](#07-집합)
    - [집합의 요소](#집합의-요소)
    - [배열로 집합만들기](#배열로-집합만들기)
        - [max](#max)
        - [num](#num)
        - [set](#set)
        - [집합 최대 개수를 반환하는 capacity 메서드](#집합-최대-개수를-반환하는-capacity-메서드)
        - [집합의 현재 요소 개수를 반환하는 size 메서드](#집합의-현재-요소-개수를-반환하는-size-메서드)
        - [요소를 검색하는 indexOf 메서드](#요소를-검색하는-indexof-메서드)
        - [요소 n이 있는지 확인하는 contains 메서드](#요소-n이-있는지-확인하는-contains-메서드)
        - [요소 n을 추가하는 add 메서드](#요소-n을-추가하는-add-메서드)
        - [요소 n을 집합에서 삭제하는 remove 메서드](#요소-n을-집합에서-삭제하는-remove-메서드)

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


# 04 스택과 큐

## 스택

스택은 데이터를 일시적으로 저장하기 위해서 사용하는 자료구조로, 데이터의 입력과 출력과 순서의 후입선출 (LIFO)입니다.스택에 데이터를 넣은 작업을 푸시라 하고, 스택에 데이터를 꺼내는 작업을 팝이라고 합니다. 가장 꼭대기를 Top 이라하고 가장 아랫부분을 Bottom 이라 합니다.

### 스택 만들기

#### 스택 본체용 배열
푸시된 데이터를 저장하는 스택 본체의 배열입니다.

```java
class IntStack {
    int max; // 스택의 용량
    int ptr; // 스택의 포인터
    int[] stk; //스택의 본체
}
```

#### 스택의 용량 : max
스택의 용량(스택의 쌓을 수 있는 최대 데이터 수)을 나타내는 필드입니다. 이 값은 배열 stk의 길이와 같습니다.

#### 스택 포인터 : ptr
스택에 쌓여 있는 데이터 수를 나타내는 필드입니다. 이 값은 스택 포인터라고 합니다.

```java
public class IntStack {
    private int max; // 스택 용량
    private int ptr;  // 스택 포인터
    private int[] stk; // 스택 본체

    public IntStack(int capacity) {
        this.ptr = 0;
        this.max = capacity;

        try {
            stk = new int[max]; //스택 본체용 배열을 생성
        } catch (OutOfMemoryError e){ // 생성할 수 없음
            max = 0;
        }
    }

    //스택이 비어 있음
    public class EmptyStackException extends RuntimeException {
        public EmptyStackException() {
        }
    }

    // 스택이 가득참
    public class OverflowStackException extends RuntimeException {
        public OverflowStackException() {
        }
    }
}
```

#### 생성자 intStack
생성자는 스택 본체용 배열을 생성하는 등 준비 작업을 수행합니다. 생성 시 스택은 비어있음으로 스택 포인터 ptr 값을 0으로 합니다. 그리고 매게변수 capacity로 전달받은 값을 스택 용량을 나타내는 max에 복사 하고 욧소수가 max인 배열을 stk의 본체를 생성합니다.

#### 푸쉬 메서드 push

```java
public int push(int x) {
    if (ptr >= max)
        throw new OverflowStackException();
    return stk[ptr++] = x;
}
```
스택에 데이터를 푸쉬하는 메서드입니다. 스택이 가득차서 푸시할 수 없는 경우에는 OverflowStackException 발생합니다.
전달받은 데이터 x를 배열 요소 stk[ptr]에 저장하고, 스택 포인터를 증가 시킵니다.

#### 팝 메서드 pop

```java
public int pop() {
    if (ptr <= 0)
        throw new EmptyStackException();
    return stk[--ptr];
}
```
스택의 꼭대기에서 데이터를 pop 하고 그 값을 반환하는 메서드입니다. 스택이 비어 있어 pop을 할 수 없는 경우 EmptyStackException 발생합니다.

#### 피크 메서드 peek
```java
public int peek() {
    if (ptr <= 0)
        throw new EmptyStackException();
    return stk[ptr - 1];
}
```
스택의 꼭대기에 있는 데이터를 몰래 엿보는 메서드입니다. 스택이 비어 있지 않으면 꼭대기의 요소 stk[ptr -1]의 값을 반환합니다. 이때 데이터의 입출력이 없음으로 스택 포인터는 변화하지 않습니다.

```java
public int indexOf(int x) {
    for (int i = ptr - 1; i >= 0; i--)
        if (stk[i] == x)
            return i;
    return -1;
}

public void clear() {
    ptr = 0;
}

public int capacity() {
    return max
}

public int size() {
    return ptr;
}

public boolean isEmpty() {
    return ptr <= 0;
}

public boolean isFull() {
    return ptr >= max;
}

public void dump() {
    if (ptr <= 0){
        System.out.println("스택이 비어있습니다");
    } else{
        for (int i = 0; i < ptr; i++)
            System.out.println(stk[i]);
    }
}
```

#### 검색 메서드 indexOf
스택 본체의 배열 stk에 x와 같은 값의 데이터가 포함되어 있는지, 포함되어 있다면 배열의 어디에 들어 있는지를 조사하는 메서드입니다.

검색은 꼭대기 쪽에서 바닥쪽으로 선형 검색을 수행합니다. 검색에 성공하면 찾아낸 요소의 인덱스를 반환하고, 실패하면 -1를 반환합니다.

꼭대기 쪽에서 스캔하는 이유는 먼저 팝이 되는 데이터를 찾기 위해서입니다. 

#### 스택의 모든 요소를 삭제 하는 메서드 clear
clear 메서드는 스택에 쌓여 있는 모든 데이터를 삭제하는 메서드입니다.

#### 용량을 확인하는 메서드 capacity
capacity 메서드는 스택의 용량을 반환하는 메서드입니다. max 값을 그대로 반환합니다.

#### 데이터 수를 확인하는 메서드 size
size 메서드를 현재 스택에 쌓여 있는 데이터 수(ptr의 값)을 반환하는 메서드입니다.

#### 스택이 비어있는지 검사하는 메서드 isEmpty
isEmpty 메서드는 스택이 비어 있는지 검사하는 메서드입니다. boolean 타입을 반환합니다.

#### 스택이 가득 차 있는지 검사하는 메서드 isFull
스택이 가득찼는지 여부를 boolean 타입으로 반환합니다.

#### 스택 안에 있는 모든 데이터를 표시하는 메서드 dump
스택에 쌓여있는 모든 데이터를 바닥에서 꼭대기 순으로 표시


## 큐
큐는 스택과 마찬가지로 데이터를 일시적으러 쌓아 놓는 자려구조 입니다. 하지만 가장 먼저 넣은 데이터를 가장 먼저 꺼내는 FIFO 점이 스택과 다릅니다.


### 배열로 큐 만들기

* 인큐
    * 데이터를 넣는 것을 인큐라고 합니다.
    * 이 처리 복잡도는 O(1)으로 적은 비용으로 구현할 수 있습니다.
* 디큐
    * 가장 앞에 있는 것을 빼내는 것을 디큐라고 합니다.
    * 꺼낸 다음 두 번째 이후의 요소를 모두 한칸씩 이동 해야하기 때문에 이 처리 복잡도는 O(n)입니다. 데이터를 꺼낼 때마다 이런 처리를하게 되면 효율이 떨어집니다.

### 링 버퍼로 큐 만들기
배열 요소를 앞쪽으로 옴ㄹ기지 않은 큐들 구현해 보겠습니다. 이를 위해 사용하는 자료구조가 링 버퍼 입니다. 링 버퍼는 처음이 끝과 연결되었다고 보는 자료구조입니다.

여기서 논리적으로 어떤 요소가 첫 번쨰 요소이고 어떤 요소가 마지막 요소인지를 식별하기 위한 변수가 Front, Rear 입니다.

* Fornt: 맨 처음 요소의 인덱스
* Rear : 맨 끛 요소의 하나 뒤의 인덱스

```java
public class IntQueue {
    private int max; // 큐의 용량
    private int font; // 첫 번쨰요소 커서
    private int rear; // 마지막 요소 커서
    private int num; // 현제 데이터 수
    private int[] que; // 큐 본체

    public IntQueue(int capacity) {
        num = font = rear = 0;
        max = capacity;
        que = new int[max];
    }

    public class EmtpyQueueExceiption extends RuntimeException {
        public EmtpyQueueExceiption() {
        }
    }

    public class OverflowQueueExceiption extends RuntimeException {
        public OverflowQueueExceiption() {
        }
    }
}
```
1. 큐로 사용할 배열 : 인큐하는 데이터를 저장하기 위한 본체용 배열
2. 큐의 최대 용량 : 큐의 최대 용량을 저장하는 필드로, 이 값은 배열 que에 저장할 수 있는 최대 요소의 개수와 같습니다.
3. Front : 인큐 하는 데이터 가운데 첫 번 쨰 요소의 인덱스를 저장하는 필드입니다. 
4. 리어 : 인큐한 데이터를 가운데 맨 나중에 넣은 요소의 하나 뒤인 인덱스를 저장하는 필드입니다.
5. 현재 데이터 수 : 큐에 쌓아 놓은 데이터 수를 나타내는 필드입니다. fornt와 rear의 값이 같은 경우 큐가 비어 있는지, 가득 찼는지 구별할 수 없는 상황을 피하기 위해 이 변수가 필요 합니다. 비어 있을때는 num이 0이고 가득 찼을 경우에는 num은 max와 같습니다.

#### 인큐 메서드 enque

```java
public int enque(int x) {
    if (num >= max)
        throw new OverflowQueueException();

    que[rear++] = x;
    num++;
    if (rear == max) rear = 0;
    return x;
}
```
큐데 데이터를 인큐 하는 메서드입니다. 인큐에 성공하면 인큐한 값을 그래도 반환합니다. 그러나 큐가 가득 차서 인큐 할 수 없으면 OverflowQueueExceiption 발생합니다.

#### 디큐 메서드 deque
```java
public int deque() {
    if (num <= 0)
        throw new EmptyQueueException();

    int x = que[font++];
    num--;
    if (font == max) font = 0;
    return x;
}
```
큐에서 데이터를 디큐하고 그 값을 반환하는 메서드입니다. 그러나 큐가 비어 있어 디큐할 수없으면 EmptyQueueException이 발생합니다.
que[font]에 저장한 값을 꺼내고 fornt 값을 1 만큼 증가 한다음 num 값을 1만큼 감소합니다.
1만큼 증가한 fornt 값이 큐의 용량인 max와 같아지면 fonrt 값은 배열의 처음인 0으로 변경합니다.

```java
public int peek() {
    if (num <= 0)
        throw new EmptyQueueException();

    return que[font];
}

public int indexOf(int x) {
    for (int i = 0; i < num; i++) {
        int idx = (i + font) % max;
        if (que[idx] == x)
            return idx;
    }
    return -1;
}

public void clear() {
    num = font = rear = 0;
}

public int capacity() {
    return max;
}

public int size() {
    return num;
}

public boolean isEmpty() {
    return num <= 0;
}

public boolean isFull() {
    return num >= max;
}

public void dump() {

    if (num <= 0) {
        System.out.println("큐가 비어 있습니다.");
    } else {
        for (int i = 0; i < num; i++) {
            System.out.println(que[i + font] % max);
        }
    }
}
```


#### 검색 메서드 indexOf
큐의 배열에서 x와 같은 데이터가 저장되어 있는지 위치를 알아 내는 메서드입니다. 프런트에서 리니어쪽으로 선형 검색을 수행합니다. 
스캔의 시작은 배열의 첫요소가 아니라 큐의 첫 요소, **프런트입니다. 그래서 스캘할 때 주목하는 인덱스는 idx 계산이 (i + fornt )/ % max**입니다.
#### 모든 데이터를 출력하는 메서드 dump
큐에 인큐된 모든 데이터를 프런트에서 리어 순으로 출력하는 메서드입니다. 큐가 비어 있으면 큐가 비어있습니다. 라고 표시합니다.


# 07 집합
집합이란 명확한 조건을 만족하는 자료의 모임을 의미합니다. 즉, 집합도 자료구조로 표현할 수 있습니다.

## 집합의 요소
집합이란 객관적으로 범위를 규정한 '어떤 것'의 모임이며, 그 지합 안에서 각각의 어떤 것들을 요소라고 부릅니다

## 배열로 집합만들기

```java
public class InetSet {
    private int max; // 집합의 최대 크기
    private int num; // 집합의 요소 개수
    private int[] set; // 집합 본체 

    // 집합의 쵀대 개수
    public int capacity() {
        return max;
    }

    // 집합의 쵀대 개수
    public int size() {
        return num;
    }

    // 집합에서 n을 검색합니다.
    public int indexOf(int n) {
        for (int i = 0; i < num; i++)
            if (set[i] == n)
                return i;
        return -1;
    }

    // 집합에 n 이 있는지 없는지 확인합니다.
    public boolean contains(int n) {
        return indexOf(n) == 1;
    }

    // 집합에 n을 추가합니다.
    public boolean add(int n) {
        if (num >= max || contains(n)) {
            return false;
        } else {
            set[num++] = n;
            return true;
        }
    }

    public boolean remove(int n) {
        int idx;

        if (num <= 0 || (idx = indexOf(n)) == -1) {
            return false;
        }else{
            set[idx] = set[--num];
            return true;
        }
    }
}
```

### max
집합의 최대 크기를 나타내는 필드입니다.

### num
집합의 요소 개수입니다. 집합의 요소는 배열의 앞쪽 부터 0 ~ num - 1 까지 넣습니다.

### set
집합을 저장할 배열입니다. 

### 집합 최대 개수를 반환하는 capacity 메서드
집합이 가질 수 있는 가장 큰 값을 반환하는 메서드로 max를 반환합니다.

### 집합의 현재 요소 개수를 반환하는 size 메서드
집합의 현재 요소 개수를 반환하는 메서드로 num의 값을 반한환합니다.

### 요소를 검색하는 indexOf 메서드
집합 배열 set에 n이 있는지 선형 검색으로 확인합니다.

### 요소 n이 있는지 확인하는 contains 메서드
집합 배열 set에 n이 들어 있는지 확인 하는 메서드

### 요소 n을 추가하는 add 메서드
요소 n을 집합에 추가하는 메서드입니다. 같은 요소가 없을 경우 추가됩니다.

### 요소 n을 집합에서 삭제하는 remove 메서드
집합에서 요소 n을 삭제하는 메서드.
먼저 indexOf 메서드에서 삭제할 수 읐는 요소 인뎃스를 idx에 대입 합니다. 그런 다음 요소 개수 num을 -1 감소 시키고 마지막 요소은 set[num]을 set[idx]에 복사합니다.