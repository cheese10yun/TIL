# npm sudo 권한으로 설치시 오류 해결


NPM으로 모듈을 설치할 경우에 `sudo` 권한으로 설치할 경우 아래와 같은 오류가 발생할 경우가 있다.

```
-bash: sodu: command not found
```

```
sudo ln -s /usr/local/bin/node /usr/bin/node
sudo ln -s /usr/local/lib/node /usr/lib/node
sudo ln -s /usr/local/bin/npm /usr/bin/npm
sudo ln -s /usr/local/bin/node-waf /usr/bin/node-waf
```

위의 명령어를 입력하면 sudo npm으로 설치를 진행할 수 있다.
