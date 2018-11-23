## 스프링 부트 테스트

| 어노테이션      | 설명                         | Bean            |
|-----------------|------------------------------|-----------------|
| @SpringBootTest | 통합 테스트, 전체            | Bean 전체       |
| @WebMvcTest     | 단위 테스트, Mvc 테스트      | MVC 관련된 Bean |
| @DataJpaTest    | 단위 테스트, Jpa 테스트      | JPA 관련 Bean   |
| @RestClientTest | 단위 테스트, Rest API 테스트 | 일부 Bean       |
| @JsonTest       | 단위 테스트, Json 테스트     | 일부 Bean       |


### @SpringBootTest
* `@SpringBootTest`는 통합 테스트를 제공하는 기본적인 스프링 부트 테스트 어노테이션
* `@SpringBootTest`는 실제 구동되는 애플리케이션과 똑강이 애플리케이션 컨텍스트를 로드하여 테스트 하기 때문에 모든 빈들을 메모리에 다 올립니다.
* 애플리케이션 설정된 빈을 모두 로드하기 때문에 테스트 구동 시간이 오래 걸립니다.


```java
@RunWith(SpringRunner.class)
@SpringBootTest
public class CartControllerTest {

    @Autowired
    private WebApplicationContext context;

    private MockMvc mockMvc;

    @Before
    public void setUp() throws Exception {
        this.mockMvc = MockMvcBuilders.webAppContextSetup(this.context)
                .build();
    }

    @Test
    public void getCarts() throws Exception {

        mockMvc.perform(get("/carts/{id}", 1L)
                .contentType(MediaType.APPLICATION_JSON))
                .andDo(print())
                .andDo(document.document(
                        pathParameters(parameterWithName("id").description("cart id"))
                ))
                .andExpect(status().isOk())
                .andExpect(jsonPath("$.cartProducts", is(notNullValue())))
                .andExpect(jsonPath("$.cartProducts[0].product", is(notNullValue())))
        ;
    }
}
```

### @WebMvcTest
* MVC를 위한 테스트입니다. 웹에서 테스트하기 힘든 컨트롤러를 테슽츠하는데 적합합니다.
* 웹상에 요청, 응답, 시큐리티, 필터까지 자동으로 테스트하며 수동으로 추가 삭제 할 수 있습니다.
* **@WebMvcTest는 MVC관련된 @Controller, @ControllerAdvice, @JsonCompoent, Filter, WebMvcConfigure, HanlderMethodArgumentResolver 만도르디 되기때문에 가볍게 테스타 할 수 있습니다.**
* `WebMvc`를 테스트 하는 단위 테스트입니다. 모든 것을 테스트하는 것을 테스트의 범위가 넓어 지는 것이며 해당 단위 테스트 위주로 테스트하는 것이 다양한 장점들이 있습니다.

```java
@RunWith(SpringRunner.class)
@WebMvcTest(MemberApi.class)
@ContextConfiguration(classes = ApiApplication.class)
public class MemberApiTest {

    @Autowired
    private MockMvc mvc;

    @Autowired
    private WebApplicationContext context;

    @MockBean
    private MemberSignUpService memberSignUpService;

    @Before
    public void setUp() throws Exception {

        this.mvc = MockMvcBuilders.webAppContextSetup(this.context)
                .build();
    }

    @Test
    public void signUp() throws Exception {
        //given
        final Email email = Email.of("test@asd.com");


        final SignUpRequest dto = SignUpRequest.builder()
                .email(email)
                .build();

        //when
        when(memberSignUpService.doSignUp(any())).thenReturn(member);

        //then
        final ResultActions resultActions = mvc.perform(post("/members")
                .contentType(MediaType.APPLICATION_JSON)
                .content(signUpRequest(dto.getEmail().getValue())))
                .andDo(print());

        resultActions
                .andExpect(status().isOk());

    }
}
```
* `@MockBean` 어노테이션으로 Mock 기반으로 해당 의존성을 주입합니다.
* `when()` 메서드를 통해서 받고자 하는 값을 Mocking 합니다.

### @DataJpaTest
* `@DataJpaTest`는 JPA 관련된 테스트 설정만 로드합니다.
* 데이터소스의 설정이 정상 적인지, JPA를 사용하여 데이터를 제대로 생성, 수정, 삭제 하는 지 등의 테스가 가능합니다.
* 내장 데이터베이스를 사용해 실제 데이터베이슬 사용하지 않고도 테스트 데이터베이스로 테스트 할 수 도 있습니다.


```java
@RunWith(SpringRunner.class)
@DataJpaTest
@ActiveProfiles("local")
public class ProductRepositoryTest {

    @Autowired
    private ProductRepository productRepository;
    
    @Test
    public void save() {
        final Product product = Product.builder()
                .name("name")
                .provider("123")
                .shipping(Shipping.freeShipping())
                .build();

        final Product save = productRepository.save(product);

        assertThat(save.getShipping().getPrice(), is(product.getShipping().getPrice()));
        assertThat(save.getShipping().getMethod(), is(product.getShipping().getMethod()));
    }
}
```
* `@ActiveProfiles("local")` local 환경으로 테스트 진행, H2 설정이 Local
* `productRepository`에대한 테스트 코드 진행