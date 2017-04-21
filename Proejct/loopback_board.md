# LoopBack Framework 기반 게시판

## 작업순서
1. 게시글 CRUD
2. 댓글 CRUD
3. 관계설정
3. ACL 설정
4. Mysql 변경
5. 해당 게시물의 댓글 가져오기
6. 뷰화면 작업하기

## 더미 데이터

```
{
"email":"asd@asd.com",
"password":"asdasd"
}
```
* Sl4ES7MxNZIHeQyV62dVgj91dESNzYJtEbUPCPyw6bLUbEqh9axHgn2sOYqfk81m

```
{
  "email": "asd@asd.com",
  "password": "asd"
}
FCFazjBsN3vPtBADpVPFqgllPb1Ud1bkLumeGmLYdf0TMwhNCrBrRRvSkAb3NUjN
```

```
{
  "title": "new",
  "username": "asdasd",
  "created_dt": "2017-03-23T10:20:42.671Z",
  "contents": "new 123123123"
}
```
{
  "email": "foo@bar.com",
  "password": "foobar"
}

---------------

{
  "realm": "string",
  "username": "string",
  "email": "asd@asd.com",
  "emailVerified": false,
  "id": "58d4ca9807cbd8adedb81067",
  "coffeeShopId": 0
}

{
  "id": "Os7z5D9hlpeyf8bdc5gKUrEhWbrRdD8l9g1o1OLjDgqslpL8fAV9tqTELxzgfjFa",
  "ttl": 1209600,
  "created": "2017-03-24T07:31:23.491Z",
  "userId": "58d4ca9807cbd8adedb81067"
}

`id` = `publisherId`

## Models
* Post - 게시글
*
  * username
  * title
  * comments
  * created_dt
* Comment - 댓글
  * username
  * comments
  * created_dt
  * post_id : FK Key(Post)

## Relations
* Post는 여려개의 Comment를 가질 수 있습니다.
* Comment는 Post에 속합니다.

## Access
* 모든 사용자는 게시글, 댓글을 읽을 수 있습니다.
* 로그인 사용자만이 게시글, 댓글을 작성할 수 있습니다.
* 본인이 작성한 게시글, 댓글만 수정, 삭제할 수 있습니다.




```
post
{
  "title": "new",
  "username": "asdasd",
  "created_dt": "2017-03-23T10:20:42.671Z",
  "contents": "new 123123123",
  "data": 1490322115477,
  "publisherId": 1,
  "userId": 1
}
put
{
  "title": "new",
  "username": "asdasd",
  "created_dt": "2017-03-23T10:20:42.671Z",
  "contents": "new 11123123123",
  "data": 1490327093409,
  "publisherId": 1,
  "userId": 1
}
login
{
  "email": "asd@asd.com",
  "password": "asd"
}

{
  "id": "G1Ae41010VE1fQ9zwcUVBKHikuejMVeuyBdgsqUmM7rOHtQMVbsG749ZeeGJif2Y",
  "ttl": 1209600,
  "created": "2017-03-24T03:18:23.459Z",
  "userId": 1
}

{
  "title": "string",
  "username": "string",
  "created_dt": "2017-03-24T03:18:08.402Z",
  "contents": "string",
  "id": 1,
  "userId": 1,
  "data": 1490325640851,
  "publisherId": 1
}


```


* 글을 작성할때 해당 유저의 유조 아이디를 게시글에 저장



* 게시글은 작성자에 속합니다.
* 작성자는 많은 게시글을 가질수 있습니다.
* 댓글은 작성자에 속합니다.
* 댓글은 게시글에 속헙니다.
* 작성자는 많은 댓글을 가질수 있습니다.

### 주의할것
* 데이터 칼럼이름이 id로 했을 경우 이상한 문제가 생김 아마도 id는 유니크한 값을 판단하기위해서 사용하는듯 post_id 이런식으로 사용하지말고 카멜케이스 postId로 사용할것
