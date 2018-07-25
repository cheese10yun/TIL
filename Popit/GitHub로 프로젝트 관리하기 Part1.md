# 프로젝트 소개 
Github를 이용해서 Project Management 하는 방법 및 전체적인 프로세스에 대해서 정리했습니다. **issue 관리, 일정 관리, 코드리뷰, 버그 리포트 등 다양한 일들을 Github 하나에서 다 관리할 수 있고 어느 하나 부족하다고 생각하지 않습니다.** 이미 Remote Repository로 Github를 사용 중 이리 사면 적극 추천해 드립니다. 프로젝트 전체 코드는 [GitHub](https://github.com/cheese10yun/github-project-management)에 공개 되어있습니다.

# 목차
* [Part1 - 이슈 발급 부터 코드리뷰까지](https://www.popit.kr/github%EB%A1%9C-%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8-%EA%B4%80%EB%A6%AC%ED%95%98%EA%B8%B0-part1-%EC%9D%B4%EC%8A%88-%EB%B0%9C%EA%B8%89-%EB%B6%80%ED%84%B0-%EC%BD%94%EB%93%9C%EB%A6%AC%EB%B7%B0%EA%B9%8C/)
* [Part2 - CI & Test Coverage & Wiki](https://www.popit.kr/github%EB%A1%9C-%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8-%EA%B4%80%EB%A6%AC%ED%95%98%EA%B8%B0-part2-ci-test-coverage-wiki/)
* Part3 - ZenHub 사용법


# 전체 플로우
1. Isuee 발급
2. Issue 작업
3. Pull Request Coide Review 진행
4. Issue 반영

# Issue 발행

## Issue란?
모든것이 이슈라고 볼 수 있습니다. 새로운 추가될 가능, 개선 해야할 가능, 버그 등등 모든것이 이슈라고 볼 수 있습니다. 모든 활동 내역에 대해서 이슈를 등록하고 그 이슈기반으로 작업을 진행하게 됩니다.

이슈를 등록할 때 자주 사용하는 템플릿을 등록해서 사용하는 방법이 효율적입니다. 이슈 템플릿을 등록하는 방법을 소개해드리겠습니다.

## Issue Template

### Issue Template 등록
![](https://github.com/cheese10yun/github/blob/master/images/issue-template-rg.gif?raw=true)

환경에 앎맞는 Issue Template 생성을 합니다.


### Issue Template 사용법
![new_issue](https://github.com/cheese10yun/github/blob/master/images/add-new-issue.gif?raw=true)

위에서 등록한 Issue Template 기반으로 이슈를 생성할 수 있습니다.


### Issue Template 파일

```
.
├── .github
│   └── ISSUE_TEMPLATE
│       ├── bug_report.md
│       └── feature_request.md
├── README.md
├── github.iml
├── images
├── mvnw
├── mvnw.cmd
├── pom.xml
└── src
```
위에서 등록된 Issue Template은 .github/ISSUE_TEMPLATE 디렉터리에 생성된 것을 확인할 수 있습니다.
**각자의 맞는 한경에 따라서 Issue Template를 작성하시면 됩니다.** 저 같은 경우에는 Back-end를 주로 담당하기 때문에 bug tempalte 에서 서버로그, response body 값을 등록했습니다.


# Issue 작업

## 등록된 issue 살펴 보기
![등록된-이슈](https://i.imgur.com/2ciNoCd.png)

* Assignees : 해당 작업의 담당자
* Labels: 해당 작업의 성격
* Milestone: 해당 작업이 속한 파트

![](https://i.imgur.com/DkniJHn.png)

다른 것들은 이해하기 쉬울 텐데 Milestone은 조금 생소할 수 있습니다. Milestone에 간단하게 설명해 드리면 이번 출시 버전이 1.0.0 일 경우 해당 버전이든 이슈(작업) 기능 강화, 새 기능추가, 버그 기타 등등 모든 이슈를 Version 1.0.0 Milestone이라는 항목에 추가하면 위 그림처럼 Version 1.0.0에 대한 전체적인 상황을 한눈에 볼 수가 있는 장점이 있습니다.

## Issue 연동
![intellij-task](https://i.imgur.com/FtO0Xme.png)

만약 Jetbrains의 IDE를 사용하고 계신다면 Task 연동을 통해서 Github와 연동하시는 것을 적극 권장해 드립니다.

## Issue 기반 Bracnh 생성
![issue-base-branch](https://i.imgur.com/R8aFoCL.png)
위에서 언급한 Jetbrains의 Task 연동을 하지 않아도 크게 상관없습니다. Task의 갖는 가장 큰 기능은 Github 이슈 기반으로 Branch를 생성을 쉽게 도와주는 것으로 생각합니다. **즉 Github에서 생성된 Issue 기반으로 Branch를 생성하는 것이 핵심입니다.**

Github Issue는 각자의 유니크한 값인 Issue Number를 갖습니다. 또 그 Iusse Number 기반으로 Branch를 이름을 갖게 하여 해당 Branch의 명확한 작업의 의도를 갖게 할 수 있습니다.

Branch 네이밍을 통해서 해당 작업의 의도를 갖게 하는 것은 한계가 있습니다. 또 동료 개발자들이 정확히 무슨 작업을 하는지도 Branch 네이밍을 통해서 유추해내기도 어렵고, 해당 작업이 반영(머지)될 때 도 마찬가지입니다. 이러한 문제들을 Issue Number 기반으로 Branch를 생성(Issue Number Branch 네이밍에 추가)하면 아주 명확해집니다.

# Pull Request[Code Review]
[issue-1](https://github.com/cheese10yun/github/issues/1)에 대한 풀리퀘스트를 통해서 코드리뷰를 진행해 보겠습니다.

## Jetbrains Pull Request
![intellij-pull-request](https://i.imgur.com/vkNR06g.png)
만약 Jetbrains IDE를 사용하신다면 위 방법 처럼 Pull Request를 하는 방법을 권장드립니다.

## GitHub Pull Request
![github-pull-request](https://i.imgur.com/6bBTJUV.png)

Github Code 텝에서 `New Pull Request` 버튼을 클릭해서 Pull Request를 진행 합니다.

## Pull Request 작성법
![](https://i.imgur.com/3TnHt0c.png)

* 왼쪽 위에 Reviewers 톱니바퀴 버튼을 클릭해서 리뷰어를 지정합니다.
* resolved: #1(해당 Issue Number) 풀리퀘스트 요청하는 이유 즉 무슨 이슈에 대한 작업인지 명시합니다.

`resolved` 키워드를 입력하면 해당 풀리퀘스트가 master Branch에 반영되면 자동으로 close 됩니다. 자동으로 close 되는 것이 싫으시다면 issue: #[해당 Issue Number]를 작성해주세요.

이렇게 Pull Request가 생성되면 새로운 Issue Number가 부여됩니다. **즉 Pull Request도 Issue입니다.**

![issue-pull-request-연결](https://i.imgur.com/skNmpeQ.png)

**반드시 해당 풀리퀘스트가 무슨 이슈에 따른 요청인지 명시하시는 것을 권장합니다.** 그렇게 되면 위 그림처럼 해당 이슈에 #2[방금 요청한 풀리퀘스트]가 연결되어 해당 이슈가 무슨 코드로 인해서 진행됐는지 추적하기 좋습니다.

## Code Review
리뷰어가 요청받은 Pull Request로 가서 `Add your review` 버튼을 클릭합니다.

![리뷰진행](https://i.imgur.com/k11vL5w.png)
소스코드에 대한 질문 등 다양한 comment를 남기는 방식으로 pull reqeust가 진행합니다.

* Approve: 코드에 대한 의문점이 없다면 승인 .
* Comment: 간단한 피드백 제출
* Request changes: 해당 코드에 문제가 있다고 판단되며 코드를 반드시 수정 요구

위 항목은 Comment로 Submit review를 진행했습니다.

![comment-표시](https://i.imgur.com/EHnVEjU.png)
위에서 작성한 comment가 해결됬었다면 `Merge pull request` 버튼을 눌러서 해당 pull request를 반영합니다. 반영이 완료되고 해당 branch가 더는 필요 없다고 판단되시면 `Delete branch` 버튼을 통해서 Remote에 있는 Branch를 삭제할 수 있습니다.

**위에서 작성한 resolved: #1 키워드 덕분에 소스코드가 해당 Branch에 적용됐으니 자동으로 #1에 대한 이슈는 close 처리됩니다.**