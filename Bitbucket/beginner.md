# Bitbucket

## SSH Mac

```
$ ssh -v
usage: ssh [-1246AaCfGgKkMNnqsTtVvXxYy] [-b bind_address] [-c cipher_spec]
           [-D [bind_address:]port] [-E log_file] [-e escape_char]
           [-F configfile] [-I pkcs11] [-i identity_file]
           [-J [user@]host[:port]] [-L address] [-l login_name] [-m mac_spec]
           [-O ctl_cmd] [-o option] [-p port] [-Q query_option] [-R address]
           [-S ctl_path] [-W host:port] [-w local_tun[:remote_tun]]
           [user@]hostname [command]
```

* `ssh -v` 명령어가 위처럼 출력안되면 ssh 설치

```
$ ls -a ~/.ssh
.           ..          id_rsa      id_rsa.pub  known_hosts

$ pbcopy < ~/.ssh/id_rsa.pub
```
* 기존에 작성된 `id_rsa.pub `을 기반으로 ssh 인증
* `pbcopy` 명령어로 ssh값 복사

![](https://confluence.atlassian.com/bitbucket/files/304578655/755335794/1/1434638786771/add_ssh_key.png)


* Bitbucket 계정 설정에 ssh key 탭에ㅓ ssh등록

## Action
* clone
* create branch
* create pull requst
* Compare
* Fork

## clone
![](http://i.imgur.com/6gi8TGV.png)

* clone 기능 별다른건 없ㅇㅁ



## Create Branch
![](http://i.imgur.com/6gi8TGV.png)

* Branch 생성
* Remote Server에 Branch 생성
* 이 기능은 JIRA와 연동 시켜 사용하면 강력함
* 버그 이슈를 만들면 해당 버그 이슈 이름으로 Branch를 생성해줌
  * ex) 회원가입 버그 `Sign-Up-Bug` 이슈 발행
  * `Sign-Up-Bug ` Branch 자동생성
  * 해당 기능이 완료되고, 리뷰작업이 끝나고 이슈 상태를 `close` 변경하면 자동 머지
* JIRA와 연동하지 않으면 크게 사용할 기능은 아님

## create pull request
* 되게 중요 실습으로 확인


## Compare
![](http://i.imgur.com/6pbamVE.png)
![](http://i.imgur.com/pTK3jKK.png)
* 두개의 Branch를 비교 하는 항목
* 주로 코드 리뷰할때 유용하게 사용함

## Fork
* 별다른거 없음 Fork 기능으로 생각하면됨



## Navigation
* Overview
* Source
* Commit
* Branch
* Pipelines
* Issue
* WiKi

## 기타

### 커밋단위
* 커밋 하나는 하나의 의도와 의미만을 가져야합니다. 한번에 여러 파일을 수정하더라도 그 의도는 단 하나여야 한다는 것입니다. 그것이 버그 수정이든 새로운 기능 추가든
* 파일을 하나만 수정하더라도 두 개 이상의 의도가 있다면 하지 말아야 합니다. 버그 수정과 새 기능 추가를 동시에 하지 않아야 한다는 것입니다.

### 커밋 규칙
| 이름     | 설명     |
| :------ | :------- |
| fix    | 잘못된 부분 수정       |
| add       | 기능 추가       |
| mod       | 코드 수정       |
| rm       | 기능 삭제       |

* 왜 커밋 했는지 설명합니다.
* 버그 수정의 경우 원래 어떤 문제가 있었는지 설명합니다.
* 사용 중인 이슈 트래커가 있다면 해당 이슈의 링크가 포함해야합니다.

### 예시

## fix
채널 그룹화 버그 해결
   - 증상 : 채널 그룹화시 2개 이상은 그룹화가 잘 진행되나 1개는 진행되지 않은 문제 해결
   - 원인 : 1개일 경우는 타입이 String 타입이라 루프를 돌지 못했음
   - 해결 : 1개일 경우 즉 String 타입일 경우는 배열로 형변환 작업 진행

해당 이슈
* 연관 이슈 #6

### Branch 네이밍 규칙

| Branch 이름     | 설명     |
| :------------- | :------------- |
| develop-new       | 새 기능 추가가 목적인 브랜치       |
| develop-test       | 무언가를 테스트하는 브랜치(새 라이브러리, 배포 환경, 실험)       |
| develop-bug       | 버그 수정이 목적인 브랜치       |
| master       | 배포용 브랜치       |



[참고링크](https://confluence.atlassian.com/bitbucket/set-up-ssh-for-git-728138079.html)
