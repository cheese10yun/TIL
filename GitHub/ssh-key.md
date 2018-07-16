## GitHub ssh 생성하기

```
$ ssh-keygen -t -C "YourEmail@example.com"
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
```
* ssh 키 생성
* 이메일 패스워드 등록

```
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_rsa
```

* 발급받은 ssh 깃허브 프로필 / ssh에 등록

```
$ ssh -T git@github.com
Hi YourEmail@example.com! You've successfully authenticated, but GitHub does not provide shell access
```
* 해당키 ssh가 제대로 발급됬나 확인
* 위와같은 메세지가 출력되면 성공
