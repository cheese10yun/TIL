# loopback-boad

```
.
├── Dockerfile
├── README.md
├── client
│   └── README.md
├── package.json
└── server
    ├── boot
    │   └── root.js
    ├── component-config.json
    ├── config.json
    ├── datasources.json
    ├── middleware.development.json
    ├── middleware.json
    ├── model-config.json
    └── server.js
```

* 메모리디비
* 유저 모댈 생성

## 프로젝트 설정

```
? 애플리케이션 이름은 무엇입니까? loopback-board
? 프로젝트를 포함시킬 디렉토리 이름 입력: loopback-board
   create loopback-board/
     info 작업 디렉토리를 loopback-board(으)로 번경

? 사용하려는 LoopBack 버전은 무엇입니까? 3.x (current)
? 예상하는 애플리케이션 종류는 무엇입니까? api-server (A LoopBack API server with local User auth)
```


## 메모리 디비 생성

```
{
  "db": {
    "name": "db",
    "connector": "memory",
    "file": "memoryFile",
  }
}

```
* 메모리 디비 생성
* memoryFile 저장하면 서버껏다 켜도 됨

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

* 모델 PersistedModel 기반
* 메모리디비
* 특성 이름 설정 JSON  타입으로 보여주기

## 게시판 게시글 작성
* 일단 바로 되는거

## 게시판 ACL 설정
```
? ACL 항목을 적용할 모델 선택: (all existing models)
? ACL 범위 선택: 모든 메소드 및 특성
? 액세스 유형 선택: All (match all types)
? 역할 선택 All users
? 적용할 권한 선택 Explicitly deny access
```
* 일단 모든 유저 권한 X

```
? ACL 항목을 적용할 모델 선택: board
? ACL 범위 선택: 모든 메소드 및 특성
? 액세스 유형 선택: Read
? 역할 선택 All users
? 적용할 권한 선택 Explicitly grant access
```
* 조회는 모든 유저

```
? ACL 항목을 적용할 모델 선택: board
? ACL 범위 선택: 단일 메소드
? 메소드 이름 입력 create
? 역할 선택 Any authenticated user
? 적용할 권한 선택 Explicitly grant access
```
* 로그인 사용자만 생성 가능
