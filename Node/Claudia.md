## Claudia.js

## 목차
- [Claudia.js](#claudiajs)
- [목차](#%EB%AA%A9%EC%B0%A8)
- [설치](#%EC%84%A4%EC%B9%98)
- [AWS CLI](#aws-cli)
- [IAM Add permissions](#iam-add-permissions)
- [Lambda 버전 관리](#lambda-%EB%B2%84%EC%A0%84-%EA%B4%80%EB%A6%AC)
- [버전 별칭 설정](#%EB%B2%84%EC%A0%84-%EB%B3%84%EC%B9%AD-%EC%84%A4%EC%A0%95)
- [사용법](#%EC%82%AC%EC%9A%A9%EB%B2%95)
- [Claudia & Express](#claudia--express)
- [Express 앱 준비하기](#express-%EC%95%B1-%EC%A4%80%EB%B9%84%ED%95%98%EA%B8%B0)
- [AWS에 배치](#aws%EC%97%90-%EB%B0%B0%EC%B9%98)
- [AWS로 저렴하고 확장 가능한 이미지 서버 만들기](#aws%EB%A1%9C-%EC%A0%80%EB%A0%B4%ED%95%98%EA%B3%A0-%ED%99%95%EC%9E%A5-%EA%B0%80%EB%8A%A5%ED%95%9C-%EC%9D%B4%EB%AF%B8%EC%A7%80-%EC%84%9C%EB%B2%84-%EB%A7%8C%EB%93%A4%EA%B8%B0)
    - [이건 강의듣고 추측 한것](#%EC%9D%B4%EA%B1%B4-%EA%B0%95%EC%9D%98%EB%93%A3%EA%B3%A0-%EC%B6%94%EC%B8%A1-%ED%95%9C%EA%B2%83)
    - [CloudFront](#cloudfront)

* AWS Lambda, API Gateway 개발 및 배포를 쉽게 도와주는 프레임 워크

## 설치

```
$ mkdir test # 해당 디렉토리 생성
$ npm init # npm 생성
$ npm install claudia -g # claudia 설치
$ claudia --version # 버전확인
```

## AWS CLI
* AWS configure 설정편 참고

## IAM Add permissions

```
AWSLambdaFullAccess
IAMFullAccess
AWSStorageGateway
```
* `Users`에 3개 permissions 추가한다.


## Lambda 버전 관리
* 람다 함슈가 게시 될 때마다 순차 배포 번호가 할당됨
* Lambda는 배포를 앞뒤로 쉽게 롤링하고 여러 버전을 동시에 사용할 수 있습니다.
* 순차 숫자 버전 외에도 Lambda는 특정 숫자 버전에 대한 포인터라는 별칭 도 지원 합니다.
* 따라서 개발, 생산 및 테스트 환경에 단일 람다 (Lambda) 기능을 사용하기가 쉽습니다.
* 예를 들어, 새 람다 버전을 배포하고 development다음 과 같이 표시 하고 철저히 테스트 한 다음 production별칭을 동일한 숫자 버전 으로 이동하십시오

## 버전 별칭 설정
* AWS 명령 행 도구 또는 Claudia를 사용하여 버전 별명을 쉽게 설정할 수 있습니다.
* . AWS CLI 도구를 사용 aws lambda create-alias하고 aws lambda update-alias. 구성을 변경했거나 새 패키지를 업로드 한 aws lambda publish-version경우 별칭을 설정하기 전에 새 숫자 버전을 게시 해야 할 수 있습니다 .
* **Claudia는 이것을 자동화하고 람다와 동기화 된 API 게이트웨이 단계를 유지합니다.**
* **`--version`을 제공 하여 명명 된 별칭을 만들 수 있습니다.**
* **Claudia는 자동으로 버전을 게시하고 명명 된 별칭을 현재 배포 번호와 연결합니다.**

## 사용법
```
$ claudia create --region us-east-1 --version development
# 다음 명령은 배포를 업데이트 testing하고 새 버전에 별칭을 할당합니다
$ claudia update --version testing
$ claudia set-version --version production
```

* 이름이 지정된 별칭을 마지막으로 배포 된 버전에 신속하게 다시 할당 할 수도 있습니다. 예를 들어, 테스트를 마친 후 전체 배포를하지 않고 현재 배포를 프로덕션으로 승격하려는 경우에 유용합니다.


## Claudia & Express

* 기존 Express 애플리케이션을 AWS Lambda 및 API Gateway에 쉽게 배치하는 방법

## Express 앱 준비하기

* Express 애플리케이션은 더이상 TCP 포트에서 청취 할 필요가 없습니다.
* API 게이트웨이가 웹 요청을 처리합니다.
* 이에 대한 일바적인 호출을 제거하고 `app.listen` 람다 함수에서 사용 할 수 있도록 모듈에서 응용 프로그램을 설정합니다.

```
// app.listen(3000) // <-- find this line and delete it or comment it out
module.exports = app; // add this line
```

## AWS에 배치

* 이제 AWS에 Lambda 함수를 배포하고 프록시 API를 만들 수 있습니다. Claudia 2.0을 사용하면 프록시 API를 쉽게 --deploy-proxy-api만들 수 있습니다. 함수를 만드는 동안 옵션을 추가하기 만하면 됩니다. 들어 --handler, 방금 생성 한 모듈 이름을 사용하므로 ( lambda기본적으로) 추가하십시오 .handler.

```
```


## AWS로 저렴하고 확장 가능한 이미지 서버 만들기
이 20 분 분량의 동영상 자습서에서는 AWS Lambda 및 API Gateway에서 API를 작성하고 운영하는 데 도움이되는 몇 가지 유용한 정보를 배웁니다.

* 개발, 테스트 및 생산을 위해 여러 람다 버전을 관리하는 방법
* 단계 변수를 사용하여 환경 구성을 관리하는 방법
* API에서 비동기 프로세스를 처리하는 방법
* API 게이트웨이 API를 사용하여 응답 유형을 사용자 지정하는 방법
* 동적 요청 경로를 사용하여 멋진 URL을 만드는 방법
* API 키를 사용하여 무단 액세스로부터 API를 보호하는 방법
* CloudFront를 사용하여 운영 비용을 줄이고 액세스 속도를 높이는 방법

우리는 GitHub에 연결하고 저장소 통계를위한 동적 이미지를 생성하는 이미지 서버를 구축하고 있습니다. 동작에서 응용 프로그램을 보려면, 방문 RepoLabels.net을 . 완성 된 프로젝트 의 소스 코드를 GitHub에서 가져옵니다.

[강의 링크](https://claudiajs.com/tutorials/image-server.html)

### 이건 강의듣고 추측 한것

### CloudFront

* 클라우드 프론트에서 API Gateway URL 연결
* HTTPS 등 다양한 설정 가능
* `cache` 기능이 있어 변경사항이 없는 경우는 `cache` 데이터를 가져와 웹속도가 높을듯?
* `API Key`를 생성해서 클라우드프론트에서 API Key가 없는 경우 해당 API를 호출 못하게 설정할 수 도있는 거같음
* 클라우드 프론트로 설정하면 API Gateway로는 다이렉트로 접속이 안되게 할 수도있는 거같음
