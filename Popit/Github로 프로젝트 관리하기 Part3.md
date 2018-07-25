# 프로젝트 소개 
Github를 이용해서 Project Management 하는 방법 및 전체적인 프로세스에 대해서 정리했습니다. **issue 관리, 일정 관리, 코드리뷰, 버그 리포트 등 다양한 일들을 Github 하나에서 다 관리할 수 있고 어느 하나 부족하다고 생각하지 않습니다.** 이미 Remote Repository로 Github를 사용 중 이리 사면 적극 추천해 드립니다. 프로젝트 전체 코드는 [GitHub](https://github.com/cheese10yun/github-project-management)에 공개 되어있습니다.

# 목차
* [Part1 - 이슈 발급 부터 코드리뷰까지](https://www.popit.kr/github%EB%A1%9C-%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8-%EA%B4%80%EB%A6%AC%ED%95%98%EA%B8%B0-part1-%EC%9D%B4%EC%8A%88-%EB%B0%9C%EA%B8%89-%EB%B6%80%ED%84%B0-%EC%BD%94%EB%93%9C%EB%A6%AC%EB%B7%B0%EA%B9%8C/)
* [Part2 - CI & Test Coverage & Wiki](https://www.popit.kr/github%EB%A1%9C-%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8-%EA%B4%80%EB%A6%AC%ED%95%98%EA%B8%B0-part2-ci-test-coverage-wiki/)
* [Part3 - ZenHub 사용법](https://www.popit.kr/github%EB%A1%9C-%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8-%EA%B4%80%EB%A6%AC%ED%95%98%EA%B8%B0-part3-zenhub-%EC%82%AC%EC%9A%A9%EB%B2%95/)

# ZenHub 사용법

![](https://i.imgur.com/F5c78MC.png)

수 많은 이슈들이 발급되면 그것을 한눈에 보는 것은 기존 UI에서는 어렵습니다. 이런 문제를 칸반보드 형식으로 해결해주는 것이 ZenHub 입니다.

![](https://cristinasantamarina.files.wordpress.com/2015/06/zenhub-task-board.png)
Public Repository를 이용 중이라면 무료로 사용 가능합니다. Private Repository는 비용을 지급하셔 야합니다.

물론 GitHub에서 제공해주는 Proeject도 칸반보드를 제공해주어서 대안이 되지만 여러 레파지토리에 대한 표시, 애픽 기능 등 다양한 기능들을 제공해주기 때문에 개인적으로 ZenHub를 추천해 드립니다.

**설치는 크롬 확장도구에서 다운로드 받으실 수 있습니다.**

## 기본 칸반보드
![](https://i.imgur.com/GvP6XPh.png)

기본적인 구조는 New Issue, Icebox, Backlog, In Progress, Review/QA, Done을 갖습니다. 각자 본인의 프로젝트와 성향에 맞게 사용하시면 됩니다. 

저 같은 경우에는 생각나는 모든 것들을 New Issue 항목에 추가하고 우선순위가 낮고 당장 필요 없는 작업은 Icebox, 우선순위가 높은 작업은 Backlog 항목에 넣습니다. 진행 중인 작업은 In Progress 나머지 Review/QA는 Pull Request 항목, Issue가 Close 되면 자동으로 Close으로 가게 됩니다.

**이처럼 모든 항목에 대해서 Issue로 관리하고 해당 Issue Number 기반으로 Branch가 생성되면서 코드리뷰, 반영 작업 사이클이 돌아가는 구조입니다.**

## Milestone 적극 활용
![](https://i.imgur.com/3lDuAxi.png)

ZenHub 사용과 직접적인 기능은 아니지만, Milestone 여러 항목을 만들고 대부분 Icebox에 있는 issue가 아니라면 적절한 Milestone에 위치시키는 것을 권장합니다. 전체적인 작업에 진행 척도를 가시적으로 확인하기 좋은 점, 해당 Milestone(버전)에 추가되는 기능, 강화되는 기능, 수정된 버그 등을 직관적이로 표시 할 수 있습니다. 또 전체 Milestone 진척도를 알 수 있어 남은 시간도 산출하기 좋은 장점이 있습니다.

## Epic 적극 활용
![](https://i.imgur.com/dZhrATb.png)
Epic은 Milestone과 비슷하게 이해하시면 됩니다. 큰 작업(Issue)이 있다면 그 작업(Issue)을 여러 Issue로 등록하고 한 묶음으로 관리하는 것입니다.

Milestone의 장점처럼 작업에 대한 진척도를 가시적으로 표시하는 장점이 있습니다. 또 큰 작업물에 대한 작업은 여러 개발자가 나누어서 하다 보니 내가 그 기능을 작업하기 전에 선행적으로 진행되어야 하는 작업도 있을 수 있습니다. 이런 것들을 대한 피드백을 Epic을 이용하면 관리하기 편합니다.


![](https://i.imgur.com/nIMGciB.png)
해당 Issue를 Epic으로 등록 시킬 경우 Create an epic 버튼을 클릭하면 됩니다.

![](https://i.imgur.com/lN2uziy.png)
Issue를 등록할 때 오른쪽 하단의 Epic에서 등록시킬 수 있습니다.

## Issue 연결

![](https://i.imgur.com/XYa3My3.png)
Connect with an issue 기능을 통해서 이슈끼리 연결을 할 수 있습니다. **위처럼 Pull Request를 할 때 아주 유용하게 사용할 수 있습니다.**

![](https://i.imgur.com/DPrWlUd.png)
Issue 13과 해당 이슈를 작업해서 Pull Request Issue 17 번이 ZenHub 보드에서 연결돼 있는 것을 확인 할 수 있습니다. **이처럼 연관된 작업을 묶어주는 장점이 있습니다.**

## Filter 기능

![](https://i.imgur.com/4pZEYsB.png)

다양한 Filter 기능을 제공합니다. Label, Assignee, Milestone, Repo 등등이 있습니다. 해당 기능은 직관적이니
Repo를 제외하고 따로 설명을 진행하지는 않겠습니다.

Repo는 여러 Repo를 한꺼번에 보여줄 수 있습니다. 가령 **Back-end, Front-end Repository가 각각 두 개 이고 그 Repo를 한 보드에 표시할 수 있습니다.** 실무 개발에서는 여러 Repo가 있기 때문에 이것을 한 보드에서 볼 수 있다는 것은 정말 좋은 Filter 기능이라고 생각합니다.
