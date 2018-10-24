# 데이터베이스에 회원 정보 저장

[step-01: Google, Facebook 간단한 소셜 인증](https://github.com/cheese10yun/spring-security-oauth2-social/blob/master/doc/step-01.md)에서 소셜 기반으로 인증 처리를 진행했습니다. 이번 차례에서는 해당 정보를 데이터베이스에 영속화 시키는 간단한 예제로 Data JPA, H2를 이용하겠습니다.

## 테이블
![social-table](https://github.com/cheese10yun/spring-security-oauth2-social/raw/master/assets/social-table.png)

* user_connection : 소셜에서 넘겨준 프로필에 대한 테이블
* user : 해당 프로젝트의 user 테이블


전체적은 플로우는 다음과 같습니다.

1. 소셜에서 회원 인증 
2. 인증된 회원정보 데이터베이스 조회
    * 조회되는 경우 -> 기존 유저로 판단, 조화된 정보기반으로 인증처리
    * 조회되지 않은 경우 -> 신규 유저로 판단, user_connection, user 테이블 저장 저장된 정보기반으로 인증처리



## Security

```java
public class SecurityConfig extends WebSecurityConfigurerAdapter {

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        // @formatter:off
        ...
		http.antMatcher("/**").authorizeRequests().antMatchers("/", "/login**").permitAll().anyRequest()
                ...
				.addFilterBefore(ssoFilter(), BasicAuthenticationFilter.class);

		// @formatter:on
    }

    private Filter ssoFilter() {
        CompositeFilter filter = new CompositeFilter();
        List<Filter> filters = new ArrayList<>();
        filters.add(ssoFilter(google(), new GoogleOAuth2ClientAuthenticationProcessingFilter(socialService)));
        filters.add(ssoFilter(facebook(), new FacebookOAuth2ClientAuthenticationProcessingFilter(socialService)));
        filter.setFilters(filters);
        return filter;
    }

    private Filter ssoFilter(ClientResources client, OAuth2ClientAuthenticationProcessingFilter filter) {
        OAuth2RestTemplate restTemplate = new OAuth2RestTemplate(client.getClient(), oauth2ClientContext);
        filter.setRestTemplate(restTemplate);
        UserInfoTokenServices tokenServices = new UserInfoTokenServices(client.getResource().getUserInfoUri(), client.getClient().getClientId());
        filter.setTokenServices(tokenServices);
        tokenServices.setRestTemplate(restTemplate);
        return filter;
    }
}
```
[step-01: Google, Facebook 간단한 소셜 인증](https://github.com/cheese10yun/spring-security-oauth2-social/blob/master/doc/step-01.md)의 SecurityConfig 에서 조금 변경한 내용입니다.

달라진 점은 크게 없고 GoogleOAuth2ClientAuthenticationProcessingFilter, FacebookOAuth2ClientAuthenticationProcessingFilter의 클래스를 기반으로 필터에 등록시켰습니다. 이때 소셜 가입 및 로그인 처리를 담당하는 SocialService를 생성자를 통해서 주입해주었습니다.


## OAuth2ClientAuthenticationProcessingFilter

```java
public class FacebookOAuth2ClientAuthenticationProcessingFilter extends OAuth2ClientAuthenticationProcessingFilter {

    private ObjectMapper mapper = new ObjectMapper();
    private SocialService socialService;

    public FacebookOAuth2ClientAuthenticationProcessingFilter(SocialService socialService) {
        super("/login/facebook");
        this.socialService = socialService;
        mapper.configure(DeserializationConfig.Feature.FAIL_ON_UNKNOWN_PROPERTIES, false);
    }

    @Override
    protected void successfulAuthentication(HttpServletRequest request, HttpServletResponse response, FilterChain chain, Authentication authResult) throws IOException, ServletException {
        final OAuth2AccessToken accessToken = restTemplate.getAccessToken(); // 토큰 정보 가져옴
        final OAuth2Authentication auth = (OAuth2Authentication) authResult;
        final Object details = auth.getUserAuthentication().getDetails(); // 소셜에서 넘겨 받은 정보를 details에 저장
        final FacebookUserDetails userDetails = mapper.convertValue(details, FacebookUserDetails.class); // Object mapper를 이용해서 객체 변환
        userDetails.setAccessToken(accessToken); // access token 정보도 저장

        final UserConnection userConnection = UserConnection.valueOf(userDetails); // UserConnection를 userDetails 기반으로 생성
        final UsernamePasswordAuthenticationToken authenticationToken = socialService.doAuthentication(userConnection); // SocialService를 이용해서 인증 절차 진행

        super.successfulAuthentication(request, response, chain, authenticationToken);
    }

}
```
OAuth2ClientAuthenticationProcessingFilter 클래스를 상속 받아서 구현한 클래스입니다. successfulAuthentication 메서드는 소셜 인증을 성공되면 호출되는 메서드입니다. 가장 중

`restTemplate.getAccessToken()` 메서드를 통해서 해당 유저의 access token 정보를 가져옵니다.

인자로 넘겨받은 authResult 객체에 소셜에서 넘겨받은 정보를 details 객체에 넘겨받습니다. 이때 넘겨받은 정보를 ObejctMapper를 이용해서 FacebookUserDetails로 변환 합니다.

[step-01: Google, Facebook 간단한 소셜 인증](https://github.com/cheese10yun/spring-security-oauth2-social/blob/master/doc/step-01.md#%EB%82%B4%EB%B6%80-%EC%BD%94%EB%93%9C)에서 보셨듯 소셜에 넘겨주는 profile 객체는 LinkedHashMap의 형태입니다. 실제 런타임 전까지는 정확한 자료형을 확인하기가 어렵습니다. 그래서 FacebookUserDetails DTO 클래스 이용하는 것이 가독성 및 유지 보수하기 좋다고 생각합니다.

FacebookUserDetails 객체를 기반으로 UserConnection 객체를 만듭니다. UserConnection 객체는 아래에서 설명하겠습니다.


### doAuthentication 메서드

```java
public UsernamePasswordAuthenticationToken doAuthentication(UserConnection userConnection) {

    if (userService.isExistUser(userConnection)) {
        // 기존 회원일 경우에는 데이터베이스에서 조회해서 인증 처리
        final User user = userService.findBySocial(userConnection);
        return setAuthenticationToken(user);
    } else {
        // 새 회원일 경우 회원가입 이후 인증 처리
        final User user = userService.signUp(userConnection);
        return setAuthenticationToken(user);

    }
}
```
Data JPA에 대한 설명은 하지 않겠습니다. 데이터베이스에서 회원 존재 유무를 확인 후 기존 회원일 경우는 바로 인증 회원 정보를 리턴합니다. 그렇지 않고 신규 회원일 경우에는 회원 가입을 진행 시킵니다. 이때 user_conncection, user 테이블에 두 곳 모두 저장시킵니다.

```
Hibernate: 
    insert 
    into
        user_connection
        (access_token, display_name, email, expire_time, image_url, profile_url, provider, provider_id, id) 
    values
        (?, ?, ?, ?, ?, ?, ?, ?, ?)

Hibernate: 
    insert 
    into
        user
        (email, nickname, provider_id, id) 
    values
        (?, ?, ?, ?)

```
신규 가입일 경우 쿼리입니다. user_connection, user 테이블에 각각 insert가 되는 것을 확인할 수 있습니다.


## UserConnection

```java
@Table(name = "user")
@NoArgsConstructor(access = AccessLevel.PROTECTED)
@Getter
public class User {

    @Id @GeneratedValue(strategy = GenerationType.AUTO)private long id;
    ...

    @OneToOne(cascade = CascadeType.PERSIST)
    @JoinColumn(name = "provider_id", referencedColumnName = "provider_id", nullable = false, updatable = false, unique = true)
    private UserConnection social;

    @Builder
    private User(String email, String nickname, UserConnection social) {
        this.email = email;
        this.nickname = nickname;
        this.social = social;
    }

    public static User signUp(UserConnection userConnection) {
        return User.builder()
                .email(userConnection.getEmail())
                .nickname(userConnection.getDisplayName())
                .social(userConnection)
                .build();

    }
}

@Entity
@Table(name = "user_connection")
@NoArgsConstructor(access = AccessLevel.PROTECTED)
public class UserConnection {
    @Id @GeneratedValue(strategy = GenerationType.AUTO) private long id;
    @Column(name = "email") private String email;
    @Column(name = "provider") @Enumerated(EnumType.STRING) private ProviderType provider;
    @Column(name = "provider_id", unique = true, nullable = false) private String providerId;
    @Column(name = "display_name") private String displayName;
    @Column(name = "profile_url") private String profileUrl;
    @Column(name = "image_url") private String imageUrl;
    @Column(name = "access_token") private String accessToken;
    @Column(name = "expire_time") private long expireTime;

    ...

    public static UserConnection valueOf(GoogleUserDetails userDetails) {
        return UserConnection.builder()
                .expireTime(userDetails.getExpiration())
                .accessToken(userDetails.getAccess_token())
                .providerId(userDetails.getSub())
                .email(userDetails.getEmail())
                .displayName(userDetails.getName())
                .imageUrl(userDetails.getPicture())
                .provider(ProviderType.GOOGLE)
                .profileUrl(userDetails.getProfile())
                .build();
    }
}
```

코드는 길지만 말하고자 하는 것은 단순합니다. 지금 프로젝트에서는 소셜 가입 외에는 회원 인증 절차(회원가입)가 없다는 가정입니다. 

그렇다는 것은 UserConnection 객체가 만들어지는 이유는 단 한 가지입니다. Google, Facebook를 통한 회원 인증일 경우입니다. UserConnection 위의 코드를 보시면 알겠지만 UserConnection 객체를 만들 수 있는 것은 방법은 `valueOf` 객체뿐입니다. (JPA 프록시객체 때문에 protected 생성자는 만들어야 합니다.)

이 처럼 객체의 생성도 명확한 근거 외에는 생성을 제한하는 좋은 구조라고 생각합니다. 또 User 객체도 마찬가지입니다. 소셜 회원 가입 이외에는 회원가입이 없으므로 User 객체는 UserConnection를 기반으로 만드는 방법 말고 제공하지 않는 것도 마찬가지입니다.