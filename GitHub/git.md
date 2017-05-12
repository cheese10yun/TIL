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
