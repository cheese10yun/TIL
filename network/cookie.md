## Cookie 


## Cookie 사용 목적

* 세션관리 : Logins, shopping carts, game scores, or anything else the server should remember
* 개인화 : User preferences, themes, and other settings
* 트래킹 : Recording and analyzing user behavior

쿠키는 일반적인 클라이언트 측 스토리지로 사용된적이 있습니다. 쿠키가 클라이언트 측에 데이터를 저장하는 유일한 방법이었을 때는 적당한 방법이 였지만. 요즘에는 modern shorage API를 선택하는 것을 권장합니다.(localStorage, SessionStorage) 등

## Secure & HttpOnly

**HTTPS를 통해서 통신하는 경우에만 쿠키를 전송한다**

```javascript
http.createServer(function(request, response){
    console.log(request.headers.cookie);
    var cookies = {};
    if(request.headers.cookie !== undefined){
        cookies = cookie.parse(request.headers.cookie);
    }
    console.log(cookies.yummy_cookie);
    response.writeHead(200, {
        'Set-Cookie':[
            'yummy_cookie=choco', 
            'tasty_cookie=strawberry',
            `Permanent=cookies; Max-Age=${60*60*24*30}`,
            'Secure=Secure; Secure',
            'HttpOnly=HttpOnly; HttpOnly',
            'Path=Path; Path=/cookie',
            'Doamin=Domain; Domain=test.o2.org'
        ]
    });
    response.end('Cookie!!');
}).listen(3000);
```

### Secure
* Response Header 에는 `Secure=Secure; Secure` 저장되어 있습니다.
* Request Header 에는 `Secure=Secure; Secure` 전송하지 않는다.
    * **HTTPS를 사용해야만 전송한다.**
    * sessionId만 가지고 변조가 가능하기 때문에 HTTPS만 전송 가능한 `Secure` 쿠키를 통해 보안성을 높일 수 있다.

### HttpOnly
* 웹브라우저에서 자바스크립트 `document.cookie` 등을 통해서 현재 쿠키 값을 확인 할 수 있다.
* `HttpOnly` 설정을 하면 자바스크립트를 통해서 쿠키 값을 확인할 수 없게 한다.

### Path
* `Path` 옵션을 지정하면 해당 디렉토리와 그 하위 디렉토리에만 웹브라우저는 웹서버에게 전송합니다.

### Domain
* test.o2.org 해당 도메인에만 동작할 수 있습니다 `Path`와 비슷합니다.

## 참고
[HTTP 쿠키 - HTTP | MDN](https://developer.mozilla.org/ko/docs/Web/HTTP/Cookies)
[생활코딩](https://opentutorials.org/course/3387/21744)