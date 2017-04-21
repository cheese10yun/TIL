# LoopBack

## 학승 방법
1. LoopBack 개발하면서 이해 안되는 점 작성
2. 이해 안된 개념에 대해 추가 학습
3. 추가 학습이 완료되면 정리

## 학습 리스트
* ㅇ


## 개념설명

## 알게된거
```javascript
var myModel = req.app.models.Thing;
```
* `req.app.models`를 통해서 모델에 접근이 가능한거같다
* 테스트는 진행안해봄

```javascript
Post.updateAll({id: context.req.params.id}, {
  title: context.req.body.title,
  contents: context.req.body.contents,
  publisherId: context.req.accessToken.userId
});
```
* `updateAll`
