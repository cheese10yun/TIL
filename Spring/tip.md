# 스프링 팁...

* 두서 없이 그냥 생각나는 팁들 정리

# 외부 설정

# properties 설정 방법

```java
@SpringBootApplication
public class PropertiesApplication {

    public static void main(String[] args) {
        SpringApplication app = new SpringApplication(PropertiesApplication.class);
        app.setWebApplicationType(WebApplicationType.NONE);
        app.run(args);

    }
}
@Component
public class SampleRunner implements ApplicationRunner {

    @Value("${yun.name}")
    private String name;

    @Override
    public void run(ApplicationArguments args) throws Exception {

        System.out.println("=================");
        System.out.println(name);
        System.out.println("=================");

    }
}
```
```yml
yun.name : "test"
```

```bash
// 실행 결괴
=================
test
=================
```
* properties 에서 설정한 값이 출력된다.



```bash
// 커멘드라인 아규먼트 빙식
java -jar target/properties-0.0.1-SNAPSHOT.jar --yun.name="yun"

// 실행 결과
=================
yun
=================
```


* 커멘드라인 아규먼트를 이용해서 실행하면 properties 우선 순위보다 커멘드라인 아규먼트가 더 높기 떄문에 출력은 yun이 된다.




