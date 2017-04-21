# Serverless Framework

## 설치

```
$ npm install -g serverless
$ serverless --version
```

* node.js v6.6.0을 권장

## AWS - credentials

* AWS CLI를 통해서 credentials 권장
* IAM Permission ` AdministratorAccess` 필수

### aws configure 여러개 설정

```
~/.aws/credentials

[profileName1]
aws_access_key_id=***************
aws_secret_access_key=***************

[profileName2]
aws_access_key_id=***************
aws_secret_access_key=***************
```

* aws configure환경 변수를 사용할 때마다 쉽게 프로젝트간에 전환 할 수 있습니다

```
export AWS_PROFILE="profileName2" && export AWS_REGION=eu-west-1
```

## Hello World

### 1. 서비스 만들기
```
$ sls create --template aws-nodejs --path myService

```
* `myService` 디렉토리 생성 이후 Serverless template 생성

### 2. 배포
```
$ sls deploy
```
* `sls`는 `Serverless CLI` 약자


### 3. 배포된 함수 호출하기
```
$ serverless invoke -f hello
```

## Serverless 구조

### service
* AWS 람다 함수를 정의흔ㄴ 곳
* AWS 람다 함수는 호출된 파일에서 AWS 람다 함수 `event`와 AWS 람다함수 `resources`가 필요로 하는 모든 AWS 인프라 트리거합니다.
* `serverless.yml` 파일로 작성됩니다.

### 여러 service

* 단일 `service`를 사용하여 해당 프로젝트의 모든 기능을, 이벤트 및 리소스를 정의합니다.
* 그러나 응용 프로그램이 커질수록 여러 서비스로 나눠야할 필요성이 생김
* 워크플로 또는 데이터 모델로 서비스를 구성하고 이러한 워크 플로우 및 데이터 모델과 관련된 기능을 서비스로 그룹화할수 있음

```
users/
  serverless.yml # Contains 4 functions that do Users CRUD operations and the Users database
posts/
  serverless.yml # Contains 4 functions that do Posts CRUD operations and the Posts database
comments/
  serverless.yml # Contains 4 functions that do Comments CRUD operations and the Comments database
```

### create
```
# Create service with nodeJS template in the folder ./myService
serverless create --template aws-nodejs --path myService
```
* `create` 명령어로 서비스를 생성가능
* 경로를 전달하여 디렉토리를 만들고 서비스의 이름을 자동으로 지정할 수 있습니다.

### serverless.yml
* `service`구성은 `serverless.yml` 파일에서 관리
* `Serverless` 서비스 선언
* 서비스에 모든 함수 정의
* 런타임 지정 (runtime: nodejs4.3)
* 실행할 각 함수를 트리거하는 이벤트 정의 (예 : HTTP 요청)
* `events`섹션에 나열된 이벤트가 배포시 이벤트에 필요한 리소스를 자동으로 만들도록 허용합니다.
* `serverless` 변수를 사용하여 유연한 구성 가능

### example
```
service: users

provider:
  name: aws
  runtime: nodejs4.3
  stage: dev # 어떤 모드로 올릴것인지 기본 설정은 dev
  region: us-east-1 # 기존 리존을 덮어씀 (cli 명령어로 처음에 지정했던 리존을 말하는 거같음)
  profile: production # The default profile to use with this service
  memorySize: 512 # Overwrite the default memory size. Default is 1024
  deploymentBucket: com.serverless.${self:provider.region}.deploys # Overwrite the default deployment bucket
  versionFunctions: false # Optional function versioning
  stackTags: # Optional CF stack tags
   key: value
  stackPolicy: # Optional CF stack policy. The example below allows updates to all resources except deleting/replacing EC2 instances (use with caution!)
    - Effect: Allow
      Principal: "*"
      Action: "Update:*"
      Resource: "*"
    - Effect: Deny
      Principal: "*"
      Action:
        - Update:Replace
        - Update:Delete
      Condition:
        StringEquals:
          ResourceType:
            - AWS::EC2::Instance

functions:
  usersCreate: # A Function
    handler: users.create
    events: # The Events that trigger this Function
      - http: post users/create
  usersDelete: # A Function
    handler: users.delete
    events:  # The Events that trigger this Function
      - http: delete users/delete

# The "Resources" your "Functions" use.  Raw AWS CloudFormation goes in here.
resources:
  Resources:
    usersTable:
      Type: AWS::DynamoDB::Table
      Properties:
        TableName: usersTable
        AttributeDefinitions:
          - AttributeName: email
            AttributeType: S
        KeySchema:
          - AttributeName: email
            KeyType: HASH
        ProvisionedThroughput:
          ReadCapacityUnits: 1
          WriteCapacityUnits: 1
```

### handler.js

* `handler.js` 파일은 함수 코드가 포함되있다.
* 함수 코드는 `serverless.yml`이 `handler.js`파일과 여기서 내 보낸 함수 를 가리 킵니다 .

## Deploy
```
$ serverless deploy
$ serverless deploy --stage prod --region us-east-1
```

* AWS 에서 dev스테이지 및 us-east-1지역 으로 기본 배치됩니다 (다른 곳에서 지정한 경우 또는 옵션으로 추가하지 않은 경우).
