## 목차
- [목차](#목차)
- [Lesson 2: The Data Structure of ACL](#lesson-2-the-data-structure-of-acl)
  - [퍼미션(Permission, 인가 받은 권한)](#퍼미션permission-인가-받은-권한)
  - [보안 식별자(SID, Security IDentity)](#보안-식별자sid-security-identity)
  - [ACL 데이터 구조](#acl-데이터-구조)
- [Lesson 3: ACL with Spring Security - part 1](#lesson-3-acl-with-spring-security---part-1)

## Lesson 2: The Data Structure of ACL
스프링 시큐리티는 ACL을 설정하는 전용 모듈을 지원합니다. ACL에는 도메인 객체와 연결하는 ID를 비록해서 여러 개의 ACE(접근 통제 엔티티)가 들어 있습니다. ACE는 다음 두 가지 핵심 요소로 구성됩니다.

### 퍼미션(Permission, 인가 받은 권한)
ACE 퍼미션은 각 비트 값을 특성 퍼미션을 의미하는 비트 마스크 입니다. `BasePermission` 클래스는 다섯 가지 기본 퍼미션을 갖고 있습니다.

| 권한              | 비트  | 정수  |
|-----------------|-----|-----|
| READE           | 0   | 1   |
| WRITE           | 1   | 2   |
| CREATE          | 2   | 4   |
| DELETE          | 3   | 8   |
| ADMINPERMISSION | 4   | 16  |

사용하지 않은 비트를 통해서 임의로 퍼미션을 지정할 수 있습니다.

### 보안 식별자(SID, Security IDentity)

<p algin ="cneter">
    <img src ="/assets/acl-sid.png">
</p>
각 ACE는 특정 SID에 대한 퍼미션을 가집니다. SID 주체는 (PrincipalSid)일 수도 있고 퍼미션과 관련된 권한(GrantedAuthoritySid)일 수도 있습니다. 스프링 시큐리티에는 ACL 객체 모델의 정의 뿐만 아니라 이 모델을 읽고 관리하는 API도 정의도어있습니다. 또 이 API를 구현한 고성능 JDBC 구현체까지 제공합니다. 아울러 ACL을 더욱쉽게 사용할 수 있도록 접근 통제결정 거수기나 JSP 태그 같은 편의 기능도 마련되어 있어서 애플리케이션 다른 보안 장치들과 일관된 방향으로 사용할 수 있습니다.

### ACL 데이터 구조
* Security Identity - SID
* Domain Object
* ACL Entry

Domain Object는 두 개의 엔티티로 구성됩니다.
* Class - 엔티티의 실제 Java 클래스
* Object Identity - 보안을 설정하려는 엔티티의 기본 식별자입니다.

ACL Entry
* 주체가 도메인 개체에 대해 가지는 실제 사용 권한을 나타냅니다.
* 기본적으로 읽기, 쓰기, 생성, 삭제, 관리자입니다. 정수 비트 마스크로 표시되므로 32 비트가 사용 가능합니다 (5 사용됨).
* 32 비트의 사용 가능 비트가 있고 5를 사용하기 때문에 필요한 경우 새로운 맞춤형 액세스 유형을 추가 할 수 있습니다.


## Lesson 3: ACL with Spring Security - part 1

```xml
<dependency>
    <groupId>org.springframework.security</groupId>
    <artifactId>spring-security-acl</artifactId>
</dependency>

<dependency>
    <groupId>net.sf.ehcache</groupId>
    <artifactId>ehcache-core</artifactId>
    <version>2.6.11</version>
</dependency>
```
* 햐당 의존성 추가

```java
@Configuration
@EnableGlobalMethodSecurity(prePostEnabled = true)
public class LssMethodSecurityConfig extends GlobalMethodSecurityConfiguration {
    //
}

@Bean
public JdbcMutableAclService aclService() {
    JdbcMutableAclService service = 
      new JdbcMutableAclService(dataSource(), lookupStrategy(), aclCache());
    service.setClassIdentityQuery("SELECT @@IDENTITY");
    service.setSidIdentityQuery("SELECT @@IDENTITY");
    return service;
}
```
* 이제 ACL에 대한 새로운 권한 평가기를 정의 할 것입니다. 그러나 거기에 도달하기 전에 먼저 ACL을 지원할 인프라 (인프라 스트럭처)를 구축해야합니다.
* 먼저 ACL 서비스를 정의 해 봅시다. 프레임 워크에서 제공하는 공식적인 JDBC 지원 구현에 대해 알아 보겠습니다
* 한 가지주의 할 점은이 두 가지 간단한 SQL 쿼리는 MySQL에 따라 다르므로 여러 데이터베이스를 지원하는 경우 코드가 아니라 구성에 넣는 것이 좋습니다.



```java
@Bean
public LookupStrategy lookupStrategy() {
    return new BasicLookupStrategy(
      dataSource(), 
      aclCache(), 
      aclAuthorizationStrategy(), 
      permissionGrantingStrategy());
}
@Bean
public AclCache aclCache() {
    EhCacheFactoryBean factoryBean = new EhCacheFactoryBean();
    EhCacheManagerFactoryBean cacheManager = new EhCacheManagerFactoryBean();
    cacheManager.setAcceptExisting(true);
    cacheManager.setCacheManagerName(CacheManager.getInstance().getName());
    cacheManager.afterPropertiesSet();
    factoryBean.setName("aclCache");
    factoryBean.setCacheManager(cacheManager.getObject());
    factoryBean.setMaxBytesLocalHeap("16M");
    factoryBean.setMaxEntriesLocalHeap(0L);
    factoryBean.afterPropertiesSet();
    return new EhCacheBasedAclCache(
      factoryBean.getObject(), 
      permissionGrantingStrategy(), 
      aclAuthorizationStrategy())
}
@Bean
@ConfigurationProperties(prefix = "spring.datasource")
public DataSource dataSource() {
    return DataSourceBuilder.create().build();
}
```
* 이제 데이터 소스, 조회 전략 및 캐시가 필요합니다. 이것이 ehcache 의존성을 일찍 정의한 이유입니다. 이제이 3 개의 새 bean을 정의 해 보겠습니다.

```java
@Bean
public AclPermissionEvaluator aclPermissionEvaluator() {
    AclPermissionEvaluator aclPermissionEvaluator = 
      new AclPermissionEvaluator(aclService());
    return aclPermissionEvaluator;
}

@Override
protected MethodSecurityExpressionHandler createExpressionHandler() {
    DefaultMethodSecurityExpressionHandler expressionHandler =
      new DefaultMethodSecurityExpressionHandler();
    expressionHandler.setPermissionEvaluator(aclPermissionEvaluator());
    return expressionHandler;
}
```
* 권한 부여 전략 - ACL 항목을 기반으로 권한을 부여할지 여부를 실제로 사용자 정의 할 수 있습니다.
* 액세스 전략을 다루는 권한 부여 전략