## 목차
- [목차](#목차)
- [Lesson 1: How Authorization Works](#lesson-1-how-authorization-works)
  - [The Authorization Actors](#the-authorization-actors)
- [Lesson 2: The Topology of Roles and Privileges - Part 1](#lesson-2-the-topology-of-roles-and-privileges---part-1)
- [Lesson 2: The Topology of Roles and Privileges - Part 2](#lesson-2-the-topology-of-roles-and-privileges---part-2)
- [Role, Authority 차이](#role-authority-차이)
- [Lesson 3: Secure Method Invocations with AOP](#lesson-3-secure-method-invocations-with-aop)
- [Lesson 4: A Custom AccessDecisionVoter](#lesson-4-a-custom-accessdecisionvoter)

## Lesson 1: How Authorization Works

### The Authorization Actors
AccessDecisionManager에는 간단한 API가 있으며 기본 진입 점으로 결정됩니다. 모든 관련 정보를 받아 승인 결정을 내립니다.

사용할 수있는 구현은 3 가지입니다.

| 접근 통제 결정 관리자      | 접근 허용 조건                                   |
|-------------------|--------------------------------------------|
| AffirmatevieBased | 하나의 거수기만 거수해도 접근 허용                        |
| ConsensuBased     | 거수기 전원이 만장일치해야 접근 허용                       |
| UnanimousBased    | 거수기 전원이 기권 또는 찬성해야(적어도 반대하는 기수기는 없어야) 접근허용 |

AccessDecisionVoter 인터페이스를 구현하며 유저의 리소스 접근 요청에 대해 AccessDecisionVoter 인터페이스의 상수 필드로 ACCESS_GRANTED(찬성), ACCESS_ABSTAIN(기권), ACCESSS_DENIED(반대) 의사를 표명합니다.

**별도로 접근 통제 결정 관리자를 명시하지 않으면 스프링 시큐리티는 AffirmatevieBased를 기본 접근 통제 결정 관리자로 임명하고 다음 두 거수기를 구성합니다.** (configure(HttpSecurity http){...} 메서드를 기본으로 AffirmatevieBased를방식으로 동작한다는 말인거같다)

* RoleVoter : 유저 롤을 기준으로 접근 허용 여부를 거수합니다. `ROLE_` 접두어(다른 접두어 사용가능)로 시작하는 접근 속성만 처리하여 우저가 리소스 접근에 필요한 롤과 동일한 롤을 보유하고 있으면 찬성, 하나라도 부족하면 반대합니다. `ROLE_` 접근 속성이 하나라도 없는 리소스에 대해서는 기권표를 던집니다.
* AuthenticatedVoter : 유저 인증 레벨을 기준으로 접근 허용 여부를 거수하여 `IS_AUTHENTICATED_FULLY`, `IS_AUTHENTICATED_REMEMBER`, `IS_AUTHENTICATED_ANONYMOUSLY` 세 가지 접근속성만 처리합니다. 유저의 인증 레벨 리소스에 접근에 필요한 레벨 보다 높으면 찬성입니다. 인증 레벨은 전체 인증(IS_AUTHENTICATED_FULLY)이 가장 높고 다음이 자동 인증(IS_AUTHENTICATED_REMEMBERME), 익명 인증 (IS_AUTHENTICATED_ANONYMOUSLY) 순입니다.
  


## Lesson 2: The Topology of Roles and Privileges - Part 1

## Lesson 2: The Topology of Roles and Privileges - Part 2

## Role, Authority 차이

* ROLE은 역할이고, Authority는 권한으로 표현의 차이임
* ROLE은 ADMIN, Authority 는 ROLE_ADMIN 으로 표시
* ROLE이 ADMIN 이지만 Authority에게 READE, WRITE 권함만 줄 수 있고, SUPERADMIN는 ROLE이 SUPERADMIN 이지만 READE, WRITE, DELETE 권한을 줄 수 있습니다.

## Lesson 3: Secure Method Invocations with AOP

## Lesson 4: A Custom AccessDecisionVoter