
## 목차
<!-- TOC -->

- [목차](#목차)
- [OAuth2 승인 방식의 종류](#oauth2-승인-방식의-종류)
- [Authorization Code Grant Type 방식](#authorization-code-grant-type-방식)
- [Implicit Grant 방식](#implicit-grant-방식)
- [Resource Owner Password Credentials Grant 방식](#resource-owner-password-credentials-grant-방식)
- [Client Credentials Grant Type 방식](#client-credentials-grant-type-방식)
- [참고](#참고)

<!-- /TOC -->

## OAuth2 승인 방식의 종류

* Authorization Code Grant Type  : 권한 부여 코드 승인 타입
클라이언트가 다른 사용자 대신 특정 리소스에 접근을 요청할 때 사용됩니다. 리스소 접근을 위한 사용자 명과 비밀번호, 권한 서버에 요청해서 받은 권한 코드를 함께 활용하여 리소스에 대한 엑세스 토큰을 받는 방식입니다.
* Implicit Grant Type : 암시적 승인
권한 부여 코드 승인 타입과 다르게 권한 코드 교환 단계 없이 엑세스 토큰을 즉시 반환받아 이를 인증에 이용하는 방식입니다.
* Resource Owner Password Credentials Grant Type : 리소스 소유자 암호 자격 증명 타입
클라이언트가 암호를 사용하여 엑세스 토큰에 대한 사용자의 자격 증명을 교환하는 방식입니다.
* Client Credentials Grant Type : 클라이언트 자격 증명 타입
클라이언트가 컨텍스트 외부에서 액세스 토큰을 얻어 특정 리소스에 접근을 요청할 때 사용하는 방식입니다.

## Authorization Code Grant Type 방식

![oauth2-doe-grant-type](/assets/oauth2-doe-grant-type_gnojt19me.png)

* (1) 클라이언트가 파리미터러 클라이언트 ID, 리다이렉트 URI, 응답 타입을 code로 지정하여 권한 서버에 전달합니다. 정상적으로 인증이 되면 권한 코드 부여 코드를 클라이언트에게 보냅니다.
  - 응답 타입은 code, token 이 사용 가능합니다.
  - **응답 타입이 token 일 경우 암시적 승인 타입에 해당합니다.**
* (2) 성공적으로 권한 부여 코드를 받은 클라이언트는 권한 부여 코드를 사용하여 엑세스 토큰을 권한 서버에 추가로 요청합니다. 이때 필요한 파라미터는 클라이언트 ID, 클라이언트 비밀번호, 리다이렉트 URI, 인증 타입 입니다.
* (3) 마지막으로 받은 엑세스 토큰을 사용하여 리소스 서버에 사용자의 데이터를 보냅니다.

## Implicit Grant 방식

![Implicit Grant](/assets/Implicit%20Grant.png)

* (1) 클라이언트가 파리미터러 클라이언트 ID, 리다이렉트 URI, 응답 타입을 code로 지정하여 권한 서버에 전달합니다. 정상적으로 인증이 되면 권한 코드 부여 코드를 클라이언트에게 보냅니다.
  - 응답 타입은 code, token 이 사용 가능합니다.
  - **응답 타입이 token 일 경우 암시적 승인 타입에 해당합니다.**
* (2) 응답 해준 Access Token 이 유효한지 검증 요청을 합니다.
* (3) 요청 받은 Access Token 정보에 대한 검증에 대한 응답값을 돌려줍니다.
* (4) 유효한 Access Token 기반으로 Resource Server와 통신합니다.

## Resource Owner Password Credentials Grant 방식

![Resource Owner Password Credentials Grant](/assets/Resource%20Owner%20Password%20Credentials%20Grant.png)

* (1) 인증을 진행합니다. 대부분 ID, Password를 통해서 자격 증명이 진행됩니다.
* (2) 넘겨 받은 정보기반으로 권한 서버에 Access Token 정보를 요청합니다. 
* (3) Access Token 정보를 응답 받습니다. 이때 Refresh Token 정보도 넘겨 줄 수도 있습니다.
* (4) Access Token 기반으로 Resource Server와 통신합니다.

## Client Credentials Grant Type 방식

![Client Credentials Grant Type](/assets/Client%20Credentials%20Grant%20Type.png)

* (1) Access Token 정보를 요청합니다.
* (3) Access Token 정보를 응답합니다. 이때 Refresh Token 정보는 응답하지 않는 것을 권장합니다. 별다른 인증 절차가 없기 때문에 Refresh Token 까지 넘기지 않는 것이라고 생각합니다.
* (4) Access Token 기반으로 Resource Server와 통신합니다.


## 참고
* [OAuth2 이해하기](http://www.bubblecode.net/en/2016/01/22/understanding-oauth2/)
* [처음으로 배우는 스프링 부트2](http://www.hanbit.co.kr/store/books/look.php?p_code=B4458049183)