# 리눅스 잡다한것

## SSH 비밀번호 입력없이 로그인하기

```
$ ssh-copy-id -i ~/.ssh/id_rsa.pub [user]@[host]
```
* 자신의 공개키를  서버로 전송
* ssh-copy-id를 사용하면 원격 서버에 authorized_keys 파일이 없다면 생성해주고 있다면 공개키를 추가해줌

```
$ chmod 700 ~/.ssh/
$ chmod 600 ~/.ssh/authorized_keys
```
* 파일 퍼미션 지정

```
$ ssh [user]@[host]
```
* 비밀번호 없이 SSH 접속 가능


## ETC

```
$ ln -s xxx.x.x.x xxx
```
* 심볼 링크를 생성함, 