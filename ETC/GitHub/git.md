# GIT 사용 법

## Git 자주 사용하는 명령어

```
git checkout -b [Local Brunch Name] [origin/Remote Brunch Name]
ex -> git checkout -b develop-api-init origin/develop-api-init
```

* remote에 있는 brunch checkout 명령어


## git 핵심 키워드

* commit : 기록을 저장한다
* push : 서버에 보낸다
* pull : 서버에서 내려받는다.
* merge : 두개의  branch를 합친다.
* branch : 각각의 독립된 환경


## git 이그노어 무시돨때

To untrack a single file that has already been added/initialized to your repository, i.e., stop tracking the file but not delete it from your system use: git rm --cached filename

To untrack every file that is now in your .gitignore:

First commit any outstanding code changes, and then, run this command:

```
git rm -r --cached .
```
This removes any changed files from the index(staging area), then just run:

```
git add .
```
Commit it:

```
git commit -m ".gitignore is now working"
```



## Rebase
* Rebase 명령으로 한 브랜치에서 변경된 사항을 다른 브랜치에 적용할 수 있다.
* 머지할 경우 커밋 이력이 남고, 작업 분기가 많아질 경우 흐름 파악하기가 어렵다
* 이때 사용 함


## Reset & Revert

### Reset

#### hard
`git reset --hard 454..`
* 돌아가려는 이력이후의 모든 내용을 제거된다.
#### soft
`git reset --soft 454..`
* 돌아가려는 이력으로 돌아감, 이전 내용도 File Status에 기록, 커밋시 변경전 이력과 동일
* 이전 커밋 이력은 제거, 파일은 그대로
* 잔잔한 커밋들 정리할때 사용하기 좋음
#### mixed
`git reset --mixed 454..`
* **옵션을 사용하지 않으면 mixed 동작**
* soft와 거의 동일
* git add 까지 되돌림 (soft 차이점)

### Revert
`git rever 344..`
* 커밋 이력들이 날라가지 않으니 커밋 이력을 로그하기 편함

### Reset & Revert 차이
* Reset Commit을 효율적으로 관리 유용


## Stash
* 특정 branch에서 작업중에 다른 branch로 checkout 해야할 경우가 빈번
* 이 때 commit을 하지 않고는 branch checkout 불가능해서 부득이하게 commit을 남발하게 됨
* 이 때 `Stash`를 사용

```
git stash save "블라블라~ 작업중 branch이 동을 위한 stash"
git stash pop stash@\{0\}
```
##
* [stash](https://blog.outsider.ne.kr/788)



##  머지된 브랜치를 삭제하는 스크립트

```
git fetch --all -p
git branch --merged | grep -E -v "master|\*" | xargs -n 1 git branch -d
git branch -vv | grep gone | sed | awk '{print $1}' | xargs -n 1 git branch -D
```
> https://yceffort.kr/2020/01/delete-merged-branch/