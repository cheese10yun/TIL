# IoC 컨테이너 빈의 스코프

## 스코프
* 싱글톤
* 프로토타입
    * Request
    * Session
    * WebSocket

## 문제가 되는 코드

### 프토토타입 빈이 싱글톤 빈을 참조하면 ?

```java
@Component @Scope("prototype") 
public class Proto {

    @Autowired
    private Single single;
}

@Component
public class Single {

}
```

`Proto` 객체는 사용될 때마다 계속 새로운 객체를 만든다. **이때 `Single` 객체는 싱글톤 객체 이기 때문에 항상 동일한 객체이다.** 문제되지 않는다.

### 싱글톤 빈이 프로토타입 빈을 참조하면 ?

```java
@Component @Scope("prototype") 
public class Proto {
}

@Component
public class Single {

    @Autowired
    private Proto proto;
}
```
`Single` 객체는 싱글톤 객체이기 때문에 Proto 타입 객체를 한번 만들면 해당 객체를 계속 이용하게 된다. 즉 `Proto` 객체가 prototype을 의도한 것처럼 동작하지 않는다.

### 해결 방법
```java
@Component 
@Scope(value = "prototype", proxMode = ScopedProxyMode.TARGET_CLASS) 
public class Proto {
}
```

 ![proxy](/assets/proxy.png)


`Single` 객체가 `Proto` 객체를 직접 참조하면 안되기 때문에 `Proxy`를 통해서 참조하게 설정합니다. 

직접참조하면 확장에 닫혀있음 OCP와 비슷한 맥락 `Single` 객체가 직접 참조하면 바꿔줄 여지가 없어짐