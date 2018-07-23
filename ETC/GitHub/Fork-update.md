# Fork Repository 동기화 시키기

* Pull Request를 날리기전에 Fork한 Repository를 최신버전으로 업데이트 해주는것이 바람직하다.

```
$ git remote -v
origin	git@bitbucket.org:xxx/loopback-boad.git (fetch)
origin	git@bitbucket.org:xxx/loopback-boad.git (push)
```
* 현재 연결되있는 Remote Repository를 확인한다.

```
$ git remote add upstream https://"원본주소"
$ git remote -v
$ git fetch upstream
```

* `upstream` 이름으로 오리지날 Remote와 연동시킨다.
* 연결이 잘됬나 확인
* 오리지날 Remote에있는 최신버전을 내려받는다.
