# 목차
<!-- TOC -->

- [목차](#목차)
- [Lesson 1: A Simple Registration Flow](#lesson-1-a-simple-registration-flow)
    - [간단한 사용자 등록](#간단한-사용자-등록)
- [Lesson 2: Authentication using Real Users](#lesson-2-authentication-using-real-users)
    - [인증 절차를 수행하기](#인증-절차를-수행하기)
- [Lesson 3: Activate a New Account via Email](#lesson-3-activate-a-new-account-via-email)
    - [Verification Token](#verification-token)
    - [이메일 인증](#이메일-인증)
- [Lesson 4: Deal with “I forgot my password”](#lesson-4-deal-with-i-forgot-my-password)
- [Lesson 5: Doing Security Questions Right (NEW)](#lesson-5-doing-security-questions-right-new)
- [Lesson 6: Ensure Password Strength during Registration - part 1 (NEW)](#lesson-6-ensure-password-strength-during-registration---part-1-new)
- [Lesson 6: Ensure Password Strength during Registration - part 2 (NEW)](#lesson-6-ensure-password-strength-during-registration---part-2-new)

<!-- /TOC -->
# Lesson 1: A Simple Registration Flow

## 간단한 사용자 등록
```java
@Controller
class RegistrationController {

    @Autowired
    private IUserService userService;

    @RequestMapping(value = "signup")
    public ModelAndView registrationForm() {
        return new ModelAndView("registrationPage", "user", new User());
    }

    @RequestMapping(value = "user/register")
    public ModelAndView registerUser(@Valid User user, BindingResult result) {
        if (result.hasErrors()) {
            return new ModelAndView("registrationPage", "user", user);
        }
        try {
            userService.registerNewUser(user);
        } catch (EmailExistsException e) {
            result.addError(new FieldError("user", "email", e.getMessage()));
            return new ModelAndView("registrationPage", "user", user);
        }
        return new ModelAndView("redirect:/login");
    }
}
```
* 회원 가입 폼 컨트롤러 추가


# Lesson 2: Authentication using Real Users

## 인증 절차를 수행하기

```java
@AllArgsConstructor
public class LssUserDetailsService implements UserDetailsService {

    private final UserRepository userRepository;

    @Override
    public UserDetails loadUserByUsername(String email) throws UsernameNotFoundException {
        final User user = userRepository.findByEmail(email);
        if (user == null) {
            throw new UsernameNotFoundException("No User found with username " + email);
        }

        return new org.springframework.security.core.userdetails.User(user.getEmail(), user.getPassword(), true, true, true, true, getAuthorities("ROLE_USER"));
    }

    private Collection<? extends GrantedAuthority> getAuthorities(String role) {
        return Arrays.asList(new SimpleGrantedAuthority(role));
    }
}
```
* `UserRepository` 객체를 이용해서 데이터베이스 이메일로 사용자 조회
* `org.springframework.security.core.userdetails.User` 객체로 `User` 객체를 리턴
* 데이터베이스에서 조회하지 못할 경우 `UsernameNotFoundException` 예외를 던짐
* `getAuthorities()` 메서드를 통해서 `USER_ROLE` 권한 부여

```java
@Autowired
public void configureGlobal(AuthenticationManagerBuilder auth) throws Exception {
    auth.userDetailsService(userDetailsService);
}
```
* 기존 메모리 기반 인증에서 데이터베이스 인증을 위한 `configureGlobal()`메서드에   `userDetailsService` 의존성 추가

# Lesson 3: Activate a New Account via Email

## Verification Token
```java
@Entity
public class VerificationToken {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String token;
    @OneToOne(targetEntity = User.class, fetch = FetchType.EAGER)
    @JoinColumn(nullable = false, name = "user_id")
    private User user;
    private Date expiryDate;
    // standard getters and setters
}

public interface VerificationTokenRepository extends JpaRepository<VerificationToken, Long> {
    VerificationToken findByToken(String token);
}

public class User {
    ...
    @Column
    private Boolean enabled;
}
```
* `VerificationToken` 엔티티 클래스 이메일 인증을 위한 토큰 발급을 담당
* `VerificationTokenRepository`를 이용해서 이메일 인증 기반 토큰 조회
* 발급한 토큰이 이메일 인증이되면 해당 유저의 `enabled`를 true 변경

## 이메일 인증
```java
String appUrl = "http://" + request.getServerName() + ":" +
  request.getServerPort() + request.getContextPath();
String token = UUID.randomUUID().toString();
VerificationToken myToken = new VerificationToken(token, user);
verificationTokenRepository.save(myToken);
sendVerificationEmail(user, token);
```
* 등록하는 동안 사용자가 이메일을 확인할 수있는 고유 링크가있는 이메일을 발송

```java
@RequestMapping(value = "/registrationConfirm")
public
ModelAndView confirmRegistration(
  Model model,
  @RequestParam("token") String token,
  RedirectAttributes redirectAttributes) {
    VerificationToken verificationToken = userService.getVerificationToken(token);
    User user = verificationToken.getUser();
    user.setEnabled(true);
    userService.saveRegisteredUser(user);
    redirectAttributes.addFlashAttribute("message", "Your account verified successfully");
    return new ModelAndView("redirect:/login");
}
```
* 발급한 이메일의 URL를 인증할 컨트롤러
* 유저의 `Enabled`를 true 변경


# Lesson 4: Deal with “I forgot my password”

# Lesson 5: Doing Security Questions Right (NEW)

#  Lesson 6: Ensure Password Strength during Registration - part 1 (NEW)

# Lesson 6: Ensure Password Strength during Registration - part 2 (NEW)