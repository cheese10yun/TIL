```conf
server {
    listen       80 default_server;
    listen       [::]:80 default_server;
    server_name  _;
    root         /usr/share/nginx/html/;

    # Load configuration files for the default server block.
    include /etc/nginx/default.d/*.conf;
    location / {

    }

    location ~* /(login|signin) {
        root /usr/share/nginx/html/xxx/page;
    }

    location /api {
        rewrite /api/(.*) /$1 break;
        proxy_pass http://internal-was-internal-lb-862573307.ap- northeast-2.elb.amazonaws.com:8080;
        proxy_set_header Host $host; 
    }


    error_page 404 /404.html;
        location = /40x.html {
    }
    error_page 500 502 503 504 /50x.html;
        location = /50x.html {
    } 
}    
```
* location ~* /(login|signin){ ...} : nginx 요청으로 login, signin URL 값이 들어오는 경우 해당 페이지로 라우팅
* Nginx proxy_pass 설정 : GET http://{nginx-lb-dns}/api/user/login 같이 `api` 요청이 들어 왔을 경우 nginx-lb-dns 으로 프록시 해준다.
  * proxy_pass : 뒤에 오는 dns/ip 주소로 요청을 전달한다.
  * rewrite : 들어온 요청의 RUL의 규칙을 변경한다 (재작성)
    * rewrite {a}를 {b}로 변경 (정규표현식)
    * /api/user/login -> /user/login 으로 api 제거 이후 받은 요청을 private-dns 서버로 프록시
  * proxy_set_header: 전달할 요청의 header 값 설정, 웹 서버로 받은 요청은 그대로 전달