# Service, ServiceImpl 구조에 대한 고찰

예제 코드는 [GitHub](https://github.com/cheese10yun/blog-sample/tree/master/service)에 공개 되어 있습니다.

스프링을 처음 공부 할 때 많이 사용되는 Service, ServiceImpl 구조에 관해서 이야기 하려고 합니다. 저도 처음 Spring을 학습할 때 Service, ServiceImpl 구조를 그대로 타이핑하면서 학습을 하였지만, 굳이 이것을 통해서 장점을 얻지 못했기 때문에 실무에서나 개인적으로나 사용하지는 않았습니다.

Service, ServiceImpl 구조의 장점을 찾으려고 해도 명확한 답변보다는 교과서적인 답변밖에 찾지를 못했습니다. 최근에 객체지향 프로그래밍을 학습하면서 Service, ServiceImpl 구조를 한번 설명하고자 합니다. **어디까지나 저의 부족한 개념을 통한 정리이기에 때문에 비판적인 시각으로 읽어주시면 감사하겠습니다. 또 여러분들도 자신만의 정의를 내려보는 것도 좋을 거 같습니다.**


결론부터 말씀드리면 Service, ServiceImpl는 좋은 구조라고 생각하지 않습니다. 특히 도메인 객체에는 더욱더 좋지 않다고 생각합니다.

## MemberService
``` java
public interface MemberService {

    Member findById(MemberId id);

    Member findByEmail(Email email);

    void changePassword(PasswordDto.ChangeRequest dto);

    Member updateName(MemberId id, Name name);
}
```
위 와 같은 인터페이스는 좋은 구조라고 생각지 않습니다. 우선 인터페이스를 두어서 얻는 이점은 세부 구현체를 숨기고 인터페이스를 바라보게 함으로써 클래스 간의 의존관계를 줄이는 것, 다형성을 사용 하는 것 이 핵심이라고 생각합니다.


**조금 더 쉽게 정리하면 하나의 인터페이스를 구현하는 여러 구현체가 있고 기능에 따라 적절한 구현체가 들어가서 다형성을 주기 위함이 이라고 생각합니다. 또 하나의 인터페이스만 바라보니 의존관계도 줄일 수 있습니다.**

하지만 인터페이스의 하나에 구현체 하나를 두면 의존관계를 줄이는 효과도 다형성을 주는 효과도 없습니다. 그렇다면 인터페이스 하나에 구현체 하나는 반드시 나쁜 구조냐의 답에는 그렇지 않다고 생각합니다. 이 부분에 대한 설명은 아래에서 진행하겠습니다.

위의 인터페이스의 문제점은 무엇일까요? 저의 생각은 **인터페이스의 책임이 너무 많은 것이 문제라고 생각합니다.** 저 인터페이스의 구현체가 두 개 이상이 되려면 해당 구현체가 다른 기능을 가져야 합니다. findById, findByEmail, changePassword 의 메소드들이 다른 구현 클래스가 들어갔다고 해서 다른 기능을 수행해야할까요? findById의 기능은 PK 값으로 해당 Member를 찾는 것입니다. id 값이 1번인 Member가 철수라면 무슨 구현체를 쓰든 철수가 나와야 하는 너무나도 당연하죠.

그렇다는 것은 위의 메소드들은 인터페이스에 대상이 되지 않습니다. MemberService의 기능을 전부 다른 기능으로 대체 가능한 세부 구현체는 현실적으로 존재하기 어렵습니다. 예외가 있다면 테스트를 위해서 Mock 으로 추상화시킬 수는 있습니다.


## 하나의 인터페이스의 하나의 구현체

그렇다면 위의 내용은 하나의 구현체를 갖는 경우에는 인터페이스를 둘 필요가 없다는 것으로 결론을 내는 것처럼 보이지만 하나의 구현체만 갖더라도 인터페이스를 사용하는 것이 바람직하다고 생각합니다. 아래와 같은 가정으로 설명하겠습니다.

### 요구사항
* 신한 카드결제 기능이 필요하다.
* 앞으로 결제 가능한 카드가 지속해서 추가될 예정이다.


```java
public interface CardPaymentService {
    void pay();
}

public class ShinhanCardPaymentService implements Card{
    private ShinhanCard shinhanCard;

    @Override
    public void pay() {
        shinhanCard.pay(); //신한 카드 결제 API 호출
        // 결제를 위한 비지니스 로직 실행....
    }
}
```

위처럼 카드 인터페이스를 두고 신한카드 구현체를 하나만 갖지만 향후 추가 적으로 생길 여지가 있으니 인터페이스를 두는 것이 바람직합니다.

그렇다면 앞으로 추가될 여지가 없다고 판단된다면 어떻게 해야 할까요? 저의 생각은 정말 추가될 여지가 없나 에 대한 꽤 깊은 고민이 필요하다고 생각합니다. 개발 관점에서만 보는 것이 아니라 도메인 관점에서 해당 도메인을 가장 잘 이해하고 있는 분과 이야기를 충분히 하고 결론짓는 것을 추천합니다.

그런데도 추가될 여지가 없다고 판단하면 저 같은 경우에는 인터페이스를 두지 않습니다. 추가되더라도 그 때 인터페이스를 두는 것은 그다지 어렵지 않거니와 애초에 모든 변경에 대응할 수 있는 구조라는 건 없다고 생각합니다. 픽스 시킬 것은 픽스 시켜야 추상화가 되고 그 추상화 기반으로 다형성을 가질 수 있다고 저는 생각합니다.

## 인터페이스는 어떻게 두어야 하는가?

위에서 MemberService 인터페이스가 책임이 너무 크다고 했습니다. 그렇다는 건 MemberServiceImpl 또한 책임이 너무 큰 것입니다. 해당 인터페이스의 기능을 모두 구현하고 있으니 말이죠. DomainService로 두는 것은 좋은 구조라 생각하지 않습니다. 물론 그다지 중요하지 않는 도메인에는 Service로 가는 것은 크게 상관없습니다. 하지만 핵심 도메인들은 다양한 구현체를 갖게 됩니다. 위에서 설명했듯이 다양한 구현체를 같은 인터페이스를 갖게 하려면 그 책임이 작아야 합니다.


```java
public class MemberFindService {

    private MemberRepository memberRepository;

    public Member findById(final MemberId id) {
        final Member member = memberRepository.findOne(id);
        if (member == null) throw new MemberNotFoundException(id);
        return member;
    }

    public Member findByEmail(final Email email) {
        final Member member = memberRepository.findByEmail(email);
        if (member == null) throw new MemberNotFoundException(email);
        return member;
    }
}
```
위의 코드는 조회만을 위한 서비스 클래스입니다. 위의 메소드들은 일반적으로 다양한 구현체를 갖기는 어렵습니다. 그런 것들은 인터페이스에 대상이 아니라고 생각합니다.

그렇다면 비밀번호 변경 기능은 어떨까요? 비밀번호 변경 기능은 대표적으로 다음과 같습니다.
* 비밀번호 기반으로 비밀번호를 변경하는 기능
* 비밀번호를 잃어버렸을 때 다른 인증 기반으로 비밀번호를 변경하는 기능

비밀번호를 변경하는 방식이 일반적으로 2개 이상입니다. 즉 구현체를 2개 이상 갖게 되고 이럴 때 인터페이스를 두는 것이 바람직하다고 생각합니다.

```java
public interface ChangePasswordService {
    public void change(MemberId id, PasswordDto.ChangeRequest dto);
}

public class ByAuthChangePasswordService implements ChangePasswordService {
    private MemberFindService memberFindService;

    @Override
    public void change(MemberId id, PasswordDto.ChangeRequest dto) {
        if (dto.getAuthCode().equals("인증 코드가 적합한지 로직 추가...")) {
            final Member member = memberFindService.findById(id);
            final String newPassword = dto.getNewPassword().getValue();
            member.changePassword(newPassword);
            // 필요로직...
        }
    }
}

public class ByPasswordChangePasswordService implements ChangePasswordService {
    private MemberFindService memberFindService;

    @Override
    public void change(MemberId id, PasswordDto.ChangeRequest dto) {
        if (dto.getPassword().equals("비밀번호가 일치하는지 판단 로직...")) {
            final Member member = memberFindService.findById(id);
            final String newPassword = dto.getNewPassword().getValue();
            member.changePassword(newPassword);
        }
    }
}
```

ChangePasswordService 책임은 비밀번호를 변경하는 것입니다. 해당 구현체들은 본인의 비밀번호 변경의 필요한 인증방식을 구현하고 최종적으로 비밀번호를 변경하게 됩니다. 이것을 인터페이스를 둘 수 있는 이유는 인터페이스의 책임이 하나이기 때문입니다.

그렇다면 하나의 인터페이스에는 하나의 메소드만 갖게 되는 것이냐? 라는 질문을 하게 됩니다. 그렇지는 않습니다. 다시 한번 카드 예제로 돌아가겠습니다.

```java
public interface CardPaymentService {
    void pay();
    void cancel();
}
```
일반적으로 카드는 결제가 있으면 반드시 취소 기능도 함께 있습니다. 결제 취소가 되지 않는 것은 상식적으로 이해하기 어렵습니다. 그것은 신한 은행 이외의 카드사들도 당연히 결제 취소 기능이 있다는 것입니다. 그렇다면 카드 결제는 위와 같이 인터페이스를 두고 세부 구현체에 따라서 카드 결제를 진행하게 됩니다.

위에서도 말했듯이 픽시 시킬것은 픽스 시켜야합니다. 그렇기에 도메인에 대한 충분한 이해 또한 개발자가 갖춰야 합니다.

## 결론
클래스를 잘게 나누고 해당 클래스에 맞는 책임을 부여하는 것은 객체지향 프로그래밍에서 중요하다고 생각합니다. 하나의 클래스에서 너무 많은 책임을 갖게 되면 다른 클래스와의 의존관계 또한 자연스럽게 늘어나 결합 도가 증가 됩니다. 그러니 클래스를 잘게 나누는 것이 좋습니다. 아래는 제가 좋아하는 인용 구입니다.

![](http://woowabros.github.io/img/2016-08-03/tray.png)

>출처 « 로버트 C.마틴 - Clean Code 177page »
>
>작은 클래스가 많은 시스템이든 큰 클래스가 몇 개뿐인 시스템이든 돌아가는 부품은 그 수가 비슷하다. (중략) 
> “도구 상자를 어떻게 관리하고 싶은가? 작은 서랍을 많이 두고 기능과 이름을 명확한 컴포넌트를 나눠 넣고 싶은가? 아니면 큰 서랍 몇 개를 두고 모두를 던져 넣고 싶은가?”

[인용 문구, 사진 - 우아한 형제들 기술 블로그 :첫 Java 프로젝트의 생생한 후기](http://woowabros.github.io/experience/2016/08/02/first_java_project.html)

너무나도 부족한 지식으로 이것을 정의 하는 것이 부끄럽지만 제가 공부한 것과 느낀 것을 공유하고 싶었습니다. 조금이라도 도움이 되기를 기원하겠습니다.
