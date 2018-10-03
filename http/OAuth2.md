
## 목차
<!-- TOC -->

- [목차](#%EB%AA%A9%EC%B0%A8)
- [OAuth2 승인 타입](#oauth2-%EC%8A%B9%EC%9D%B8-%ED%83%80%EC%9E%85)
- [Authorization Code Grant Type : 권한 부여 코드 승인 타입](#authorization-code-grant-type--%EA%B6%8C%ED%95%9C-%EB%B6%80%EC%97%AC-%EC%BD%94%EB%93%9C-%EC%8A%B9%EC%9D%B8-%ED%83%80%EC%9E%85)
- [참고](#%EC%B0%B8%EA%B3%A0)

<!-- /TOC -->

## OAuth2 승인 타입

* Authorization Code Grant Type  : 권한 부여 코드 승인 타입
클라이언트가 다른 사용자 대신 특정 리소스에 접근을 요청할 떄 사용됩니다. 리스소 접근을 위한 사용자 명과 비밀번호, 권한 서버에 요청해서 받은 권한 코드를 함ㄲ 활용하여 리소스에 대한 엑세스 토큰을 받는 방식입니다.
* Implicit Grant Type : 암시적 승인
권한 부여 코드 승인 타입과 다르게 권한 코드 교환 단계 없이 엑세스 토큰을 즉시 반환받아 이를 인증에 이용하는 방식입니다.
* Resouce Owner Password Credentials Grant Type : 리소스 소유자 암호 자격 증명 타입
클라이언트가 암호를 사용하여 엑세스 토큰에 대한 사용자의 자격 증명을 교환하는 방식식입니다.
* Client Credentials Grant Type : 클라이언트 자격 증명 타입
클라이언트가 컨텍스트 외부에서 액세스 토큰을 얻어 특정 리소스에 접근을 요청할 때 사용하는 방식입니다.
*  Authorization Code Grant Type  : 권한 부여 코드 승인 타입

## Authorization Code Grant Type  : 권한 부여 코드 승인 타입

![oauth2-doe-grant-type](/assets/oauth2-doe-grant-type_gnojt19me.png)

* (1) 클라이언트가 파리미터러 클라이언트 ID, 리다이렉트 URI, 응답 타입을 code로 지정하여 권한 서버에 전달합니다. 정상적으로 인증이 되면 권한 코드 부여 코드를 클라이언트에게 보냅니다.
  - 응답 타입은 code, token 이 사용 가능합니다.
  - **응답 타입이 token 일 경우 암시적 승인 타입에 해당합니다.**
* (2) 성공적으로 권한 부여 코드를 받은 클라이언트는 권한 부여 코드를 사용하여 엑세스 토큰을 권한 서버에 추가로 요청합니다. 이때 필요한 파라미터는 클라이언트 ID, 클라이언트 비밀번호, 리다이렉트 URI, 인증 타입 입니다.
* (3) 마지막으로 받은 엑세스 토큰을 사용하여 리소스 서버에 사용자의 데이터를 보냅니다.


## 참고
* [처음으로 배우는 스프링 부트2](http://www.hanbit.co.kr/store/books/look.php?p_code=B4458049183)