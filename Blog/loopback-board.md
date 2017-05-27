# loopback-boad

## Loopback 게시판 만들기 (1)

Loopback 으로 게시판을 만드는 프로젝트를 진행하겠습니다. 앞선 포스팅에서 설명 드렸다시피 Loopback은 정말 빠르게 REST API를 만들어 낼 수 있습니다. 회원 가입, 로그인, 로그아웃, ACL, 설정 등 매번 구현해야 하는 귀찮은 로직들을 내부 Model을 사용해서 빠르게 만들 수 있습니다. 하지만 이렇게 말로 설명해 드리는 것보다 웹에 가장 간단한 게시판으로 포스팅을 진행하겠습니다.



## Loopback 설치
```
$ npm install -g loopback-cli
```



* 메모리디비
* 유저 모댈 생성

## 게시판 프로젝트 생성

```
$ lb
? 애플리케이션 이름은 무엇입니까? loopback-board
? 프로젝트를 포함시킬 디렉토리 이름 입력: loopback-board
   create loopback-board/
     info 작업 디렉토리를 loopback-board(으)로 번경

? 사용하려는 LoopBack 버전은 무엇입니까? 3.x (current)
? 예상하는 애플리케이션 종류는 무엇입니까? api-server (A LoopBack API server with local User auth)
```
* 터미널에서 원하시는 디렉토리 이동 이후 lb 명령어를 입력하고 프로젝트 설정을 이어 갑니다.
* 버전은 최신 버전 3.X 버전을 선택합니다.
* 애플리케이션 종류로는 `api-server (A LoopBack API server with local User auth)`를 택합니다.
* 해당 종류를 택하면 내부 `User` 기반으로 로그인, 로그아웃, 회원 가입 등등 로직을 구현 해줍니다.


## 데이터베이스 연결

```
"db": {
    "name": "db",
    "connector": "memory",
    "file": "memoryDBFile"
  }
```
* `datasources.json` 파일에 위의 내용을 추가합니다.
* 본 포스팅에서는 설치 없이 사용할수 있는 메모리 `In-memory db`를 이용하겠습니다.
* 데이터베이스를 `memoryDBFile` 파일에 저장하여 `In-memory db`를 편리하게 사용할 수 있습니다.

## 게시판 모델 생성

```
? 모델 이름 입력: board
? board을(를) 첨부할 데이터 소스 선택: db (memory)
? 모델 기본 클래스 선택 PersistedModel
? REST API를 통해 board을(를) 공개합니까? Yes
? 사용자 정의 복수형 양식(REST URL 빌드에 사용됨):
? 공통 모델 또는 서버만 해당? 공통
지금 일부 board 특성을 추가하십시오.

완료된 경우 비어 있는 특성 이름을 입력하십시오.
? 특성 이름: title
   invoke   loopback:property
? 특성 유형: string
? 필수 여부 Yes
? 기본값[없는 경우 공백으로 두기]:

다른 board 특성을 추가하십시오.
완료된 경우 비어 있는 특성 이름을 입력하십시오.
? 특성 이름: content
   invoke   loopback:property
? 특성 유형: string
? 필수 여부 Yes
```

* Loppback의 핵심 개념인 Model을 생성합니다.
* 위에서 설정한 `db`를 선택합니다.
* 기타 설정들은 위의 예시와 동일하게 설정합니다.
* 위의 작업을 진행하면 `board.json`의 `properties` 항목에 추가 됩니다.

### board.json
```
....
"properties": {
  "title": {
    "type": "string",
    "required": true
  },
  "content": {
    "type": "string",
    "required": true
  },
  "createdDt": {
    "type": "date",
    "required": false,
    "description": "생성 날짜",
    "defaultFn": "now"
  }
}
...
```
* 위에서 작성한 `title`, `content` 프로퍼티가 추가되었습니다.
* 모델을 생성할 때 프로퍼티를 생성할 수 있고, 직접 해당 모델의 json 파일에 작성할 수 있습니다.
* `createdDt` 프로퍼티는 직접 작성합니다.
* `"defaultFn": "now"` 옵션을 주면 현재 시간이 자동으로 입력됩니다.


## 게시판 ACL 설정
```
$ lb acl
? ACL 항목을 적용할 모델 선택: (all existing models)
? ACL 범위 선택: 모든 메소드 및 특성
? 액세스 유형 선택: All (match all types)
? 역할 선택 All users
? 적용할 권한 선택 Explicitly deny access
```
* Loopback은 ACL 설정도 간편합니다.
* `lb acl` 명령어로 작업을 진행합니다.
* 위 설정은 모든 모델에 대한 ACL 거부하는 설정입니다.
* 일단 막아 놓고 하나하나 풀어주는게 보안에 안전합니다.


```
$ lb acl
? ACL 항목을 적용할 모델 선택: board
? ACL 범위 선택: 모든 메소드 및 특성
? 액세스 유형 선택: Read
? 역할 선택 All users
? 적용할 권한 선택 Explicitly grant access
```
* `board`에 대한 ACL 설정을 진행합니다.
* 모든 사용자는 `board`를 읽을 수 있습니다.
* 로그인하지 않은 사용자도 게시글을 볼 수 있게하는 설정입니다.

```
$ lb acl
? ACL 항목을 적용할 모델 선택: board
? ACL 범위 선택: 단일 메소드
? 메소드 이름 입력 create
? 역할 선택 Any authenticated user
? 적용할 권한 선택 Explicitly grant access
```
* 로그인한 사용자만이 `board`를 작성할 수 있습니다.


## 게시판 ACL 설정 코드
```
...
"acls": [
    {
      "accessType": "*",
      "principalType": "ROLE",
      "principalId": "$everyone",
      "permission": "DENY"
    },
    {
      "accessType": "EXECUTE",
      "principalType": "ROLE",
      "principalId": "$authenticated",
      "permission": "ALLOW",
      "property": "create"
    },
    {
      "accessType": "READ",
      "principalType": "ROLE",
      "principalId": "$everyone",
      "permission": "ALLOW"
    },
    {
      "accessType": "WRITE",
      "principalType": "ROLE",
      "principalId": "$owner",
      "permission": "ALLOW"
    }
  ],
...
```
* 위의 ACL 작업을 진행하면 `board.json` 파일에 `"acls"` 위와 같이 추가된것을 확인할 수 있습니다.
* `"accessType": "WRITE",`은 수동으로 작성합니다.
* 해당 조건은 해당 글의 소유자만이 접근할 수 있는 설정입니다.
* 해당 글에 수정, 삭제에 관련 ACL 설정으로 보시면 편합니다.

## 회원 가입
![](http://i.imgur.com/Enk0Eic.png)
```
{
  "username": "yun",
  "email": "yun@test.com",
  "password":"123123"
}
```
* 해당 회원 가입을 Swagger 문서를 통해서 진행합니다.

## 로그인
![](http://i.imgur.com/tHepa6F.png)
```
{
  "email": "yun@test.com",
  "password":"123123"
}
```
* 로그인에 성공하면 AccessToken 값을 리턴 받습니다.
* 상단의 토큰 화면에 이 값을 입력합니다.

## 게시글 작성

![](http://i.imgur.com/G60l3fm.png)
```
{
  "title": "게시글",
  "content": "내용 블라...."
}
```

## 비 로그인 사용자가 게시글을 작성할 경우
```
{
  "error": {
    "statusCode": 401,
    "name": "Error",
    "message": "Authorization Required",
    "code": "AUTHORIZATION_REQUIRED",
    "stack": "Error: Authorization Required...
  }
}
```
* 접근권한에 대한 에러 메세지를 포함하여 `statusCode` 401를 리턴 합니다.


## 게시글 조회
![](http://i.imgur.com/8Klt8zq.png)

## 마무리
Loopback을 통해서 정말 간단하게 게시판을 만들어 보았습니다. 처음 Loopback을 공부할때는 `In-memory DB`를 추천합니다. 별다른 설정없이 데이터베이스 설정을 완료 할수 있습니다. 또한 ORM을 지원하기 때문에 이후에 다른 데이터베이스로 크게 무리 없이 변경 가능합니다. 앞으로도 Loopback 관련된 주제를 포스팅 진행해보겠습니다.
