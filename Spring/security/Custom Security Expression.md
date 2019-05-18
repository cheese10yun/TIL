# A Custom Security Expression with Spring Security

# 개요

Spring 프레임워크에서 사용 할 수 있는 Security Expression 정의해서 다양한 Expression 확장 할 수 있습니다.


# Custom Permission Evaluator

## PermissionEvaluator
우리 자신의 사용자 정의 권한 평가기를 만들려면 PermissionEvaluator 인터페이스 를 구현해야합니다.

```java
public class CustomPermissionEvaluator implements PermissionEvaluator {
    @Override
    public boolean hasPermission(
      Authentication auth, Object targetDomainObject, Object permission) {
        if ((auth == null) || (targetDomainObject == null) || !(permission instanceof String)){
            return false;
        }
        String targetType = targetDomainObject.getClass().getSimpleName().toUpperCase();
         
        return hasPrivilege(auth, targetType, permission.toString().toUpperCase());
    }
 
    @Override
    public boolean hasPermission(
      Authentication auth, Serializable targetId, String targetType, Object permission) {
        if ((auth == null) || (targetType == null) || !(permission instanceof String)) {
            return false;
        }
        return hasPrivilege(auth, targetType.toUpperCase(), 
          permission.toString().toUpperCase());
    }
}
```

## Expression

```java
@PostAuthorize("hasAuthority('USER_READ_PRIVILEGE')")
@PostAuthorize("hasPermission(returnObject, 'read')")
@PreAuthorize("hasPermission(#id, 'USER', 'read')")
```
#id 는 메서드 매개 변수를 나타내고  USER는 대상 개체 유형을 나타냅니다.

## Method Security Configuration method security configuration 
CustomPermissionEvaluator 정의 이외에도 method security configuration 설정이 필요합니다.
```java
@Configuration
@EnableGlobalMethodSecurity(prePostEnabled = true)
public class MethodSecurityConfig extends GlobalMethodSecurityConfiguration {
 
    @Override
    protected MethodSecurityExpressionHandler createExpressionHandler() {
        DefaultMethodSecurityExpressionHandler expressionHandler = 
          new DefaultMethodSecurityExpressionHandler();
        expressionHandler.setPermissionEvaluator(new CustomPermissionEvaluator());
        return expressionHandler;
    }
}
```

## Example In Practice

```java
@Controller
public class MainController {
     
    @PostAuthorize("hasPermission(returnObject, 'read')")
    @RequestMapping(method = RequestMethod.GET, value = "/foos/{id}")
    @ResponseBody
    public Foo findById(@PathVariable long id) {
        return new Foo("Sample");
    }
 
    @PreAuthorize("hasPermission(#foo, 'write')")
    @RequestMapping(method = RequestMethod.POST, value = "/foos")
    @ResponseStatus(HttpStatus.CREATED)
    @ResponseBody
    public Foo create(@RequestBody Foo foo) {
        return foo;
    }
}
```

## xample In Practice

```java
@RestController
public class MainController {
    @PostAuthorize("hasPermission(returnObject, 'read')")
    @RequestMapping(method = RequestMethod.GET, value = "/foos/{id}")
    public User findById(@PathVariable("id") long id){
        final Optional<User> user = userRepository.findById(id);
        return user.get();
    }

    @PreAuthorize("hasPermission(#foo, 'write')")
    @RequestMapping(method = RequestMethod.POST, value = "/foos")
    @ResponseStatus(HttpStatus.CREATED)
    public User create(@RequestBody User user) {
        return user;
    }
}   
```




# 참고

* [Baeldung - A Custom Security Expression with Spring Security](https://www.baeldung.com/spring-security-create-new-custom-security-expression)