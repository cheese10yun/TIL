# Gollum

## Gollum 란?

gollum은 이 git기반의 위키를 별로도 분리해서 공개한 Ruby on Rails(kroisse님의 댓글로 수정합니다. Rails없이 Sinatra기반으로 동작한답니다.)로 만들어진 솔루션입니다. git저장소를 기반으로 동작하는 위키로 페이지를 만들면 새파일을 생성하고 변경사항에 대한 설명을 커밋메시지로 하여 커밋을 하게 됩니다. 형상관리를 해주는 git의 히스토리를 위키에서 그대로 이용할 수 있으며 웹으로 띄운 gollum에서 페이지추가나 수정을 하여도 되고 에디터를 사용하여 직접 파일을 조작해도 상관없습니다. 어차피 git기반이니까요.(이글을 쓰는 시점의 최신 버전은 v1.1.1입니다.) 원격저장소를 이용할 경우 원격저장소를 등록하고 push만 하면 자동으로 백업역할까지 하게 됩니다.

사실 기능이 대단하진 않고 아주 심플한 편인데 그냥 위키의 문서화 기능에 충실한 점이 맘에 들었습니다.(사실 저도 Wiki를 잘쓰는 편은 못됩니다. 그냥 메모적는정도로만...) 그리고 웹에만 의존하지 않고 git기반이기 때문에 제가 자유롭게 접근할 수 있다는 점과 상당히 Geek스럽게 느껴지는 부분이 좋았습니다. 파일기반의 마크다운이기 때문에 나중에 다른 위키나 시스템으로 옮겨가게 될 경우에도 크게 손이 가지 않을 것 같았습니다.(플랫폼에 대한 의존성이 적다고 할 수 있겠죠.)

[Outsider's Dev Story :Gollum 출처](https://blog.outsider.ne.kr/579)

## 설치 (Mac OS 기준)

```
[sudo] gem install gollum
gem install github-markdown
```

```
Could not create Makefile due to some reason, probably lack of necessary
libraries and/or headers.  Check the mkmf.log file for more details.  You may
need configuration options
```
**위와 같은 에러 발생시에 아래의 명령어 입력**

```
brew install icu4c
or
apt-get install libicu-dev
```

## 실행
```
mkdir my_wiki
cd my_wiki
git init
touch Home.md
git add -all
git commit -m "commit"
gollum
```
`http://localhost:4567` 접속 위키 결과 확인 가능
