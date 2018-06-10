# 28. 한정적 와일드카드를 써서 API 유연성을 높여라
* 형인자 자료형은 불변 자료형이다. 다시 말해 Type1과 Type2가 있을 때 List<Type1>, List<Type2> 상이의 어떤 상위-하위 자료형 관계도성립 할 수없다.

## PECS 

PESC (Produce - Extedns, Consumer - super)

* 인자 T가 생성자라면 <? extends T> 라고 하고 T가 소비자라면 <? super T> 하라는 예이다.
