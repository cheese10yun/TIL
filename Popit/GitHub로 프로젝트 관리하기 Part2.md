# 프로젝트 소개 
Github를 이용해서 Project Management 하는 방법 및 전체적인 프로세스에 대해서 정리했습니다. **issue 관리, 일정 관리, 코드리뷰, 버그 리포트 등 다양한 일들을 Github 하나에서 다 관리할 수 있고 어느 하나 부족하다고 생각하지 않습니다.** 이미 Remote Repository로 Github를 사용 중 이리 사면 적극 추천해 드립니다. 프로젝트 전체 코드는 [GitHub](https://github.com/cheese10yun/github-project-management)에 공개 되어있습니다.

# 목차
* [Part1 - 이슈 발급 부터 코드리뷰까지](https://www.popit.kr/github%EB%A1%9C-%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8-%EA%B4%80%EB%A6%AC%ED%95%98%EA%B8%B0-part1-%EC%9D%B4%EC%8A%88-%EB%B0%9C%EA%B8%89-%EB%B6%80%ED%84%B0-%EC%BD%94%EB%93%9C%EB%A6%AC%EB%B7%B0%EA%B9%8C/)
* [Part2 - CI & Test Coverage & Wiki](https://www.popit.kr/github%EB%A1%9C-%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8-%EA%B4%80%EB%A6%AC%ED%95%98%EA%B8%B0-part2-ci-test-coverage-wiki/)
* Part3 - ZenHub 사용법

# CI & Test Coverage
![](https://i.imgur.com/G5jo0Ty.png)

[GitHub Marketplace](https://github.com/marketplace/category/continuous-integration) Public Repository를 이용하면 대부분 무료로 이용 가능합니다. **본 포스팅에서는  CI는 Travis CI, Test Coverage는 Coveralls를 이용해서 진행하겠습니다.**

전체적인 플로우를 설명하는 것이 목적 이리서 특정 툴에 대한 직접적인 사용법을 다루지는 않겠습니다. 언어의 특성 및 개인에 기호에 맞는 제품을 사용하시면 됩니다.

## 전체 플로우
1. Pull Request 요청 -> Code Review 진행
2. Code Review 완료 -> 특정 Branch에 반영
3. 특정 Branch 수정시 자동 CI Build 작업 진행 -> 테스트 코드 실행
4. 테스트 커버지리 표시

## Pull Request & Code Review
![](https://i.imgur.com/q6HmT7o.png)

별다른 설정을 하지 않았다면 Pull Request를 요청할 경우 Travis에서 자동으로 해당 요청한 코드 기반으로 Build 작업이 진행됩니다. Build가 실패했을 경우는 Pull Request 요청자는 코드를 수정해서 최소한 Build가 된 코드 기반으로 Code Review를 진행하게 해야 됩니다(Build도 안 되는 코드를 리뷰할 이유는 없을 거 같습니다.)

요청받은 Pull Request에 대해서 Code Review 작업을 진행하게 됩니다. Code Review가 완료되면 Merge pull request를 통해서 해당 작업(issue)을 반영합니다.

## 테스트 커버지리 표시
![](https://i.imgur.com/U1ROYeE.png)

위에서 Merge pull request를 통해서 해당 작업(issue)을 반영했다면 Travis가 Build 할 때 작성된 Test Code 기반으로 Coverage 정보를 위처럼 자동으로 코멘드를 추가해줍니다.

누군가가 테스트 코드를 작성하지 않았다면 `Change from base` 항목에서 - 표시가 됩니다. **이렇게 해당 작업마다 커버리지를 표시하는 것이 전체 커버리지를 높이고 그 값을 유지하는 좋은 방법이라고 생각합니다.**

# Wiki
GitHub에서는 기본적인 WIKI 기능을 제공해줍니다. 

## Wiki 등록
![](https://github.com/cheese10yun/github/blob/master/images/wiki.gif?raw=true)

## GitHub 작성
![](https://i.imgur.com/t3LuQsm.png)
GitHub에서 위키를 작성할 수 있습니다.

## Local 작성
![](https://i.imgur.com/Ff6Heeb.png)
**위키를 로컬환경에서 작성해서 Push 할 수 있습니다. 이렇게 사용하시는 것을 추천 드립니다.**