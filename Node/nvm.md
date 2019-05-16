## NVM

* 노드버전을 쉽게 변경할 수 있다.

### 노드 완전삭제
```
sudo rm -rf /usr/local/{lib/node{,/.npm,_modules},bin,share/man}/{npm*,node*,man1/node*}

sudo rm -rf /usr/local/bin/npm /usr/local/share/man/man1/node* /usr/local/lib/dtrace/node.d ~/.npm ~/.node-gyp /opt/local/bin/node opt/local/include/node /opt/local/lib/node_modules
```

To completely uninstall node + npm is to do the following:

1. go to /usr/local/lib and delete any node and node_modules
2. go to /usr/local/include and delete any node and node_modules directory
3. if you installed with brew install node, then run brew uninstall node in your terminal
4. check your Home directory for any local or lib or include folders, and delete any node or node_modules from there
5. go to /usr/local/bin and delete any node executable

[출처](http://stackoverflow.com/questions/11177954/how-do-i-completely-uninstall-node-js-and-reinstall-from-beginning-mac-os-x)



###  NVM 설치
```
$ curl https://raw.githubusercontent.com/creationix/nvm/v0.30.2/install.sh | bash

$ source ~/.bash_profile
$ nvm --version
```

* 터미널로 nvm 설치
* 쉘 재시작 명령어
* nvm 버전 확인

### NVM 사용법
```
$ nvm install stable
$ nvm install v5.5.0
$ nvm use v4.2.6
$ nvm ls
$ nvm ls-remote
$ nvm alias default v6.7.0
```
* 최신 버전 설치하기
* 특정 버전 설치하기
* 특정 버전 사용하기
* 설치된 노드 버전 리스트
* 설치할 수 있는 버전 리스트 가져오기
* 해당 버전 디폴트로 설정 (디폴트 설정을 안하게될 경우 매번 노드 버전을 nvm use 명령어를 사용해야한다.)



## 과제

### LoopBack

* part 1 ~ 2


### 게시판

#### LoopBack
* 게시판 CURD
* 로그인
* 댓글
* 스퀸시 다이어그램
* Define model relations 잘볼것
* Define access controls 잘볼것
