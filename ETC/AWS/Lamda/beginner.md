# AWS Lamda 입문

## Labmda란 ?
AWS Lambda는 2014년에 Amazon에서 새롭게 내놓은 서비스이다. Lambda 사이트에 가면 서버에 대한 걱정 없이 코드 실행, 사용한 컴퓨팅 시간에 대해서만 비용 지불라고 나와 있는데 간단히 얘기하면 Lambda 서비스에 작성한 비즈니스 로직을 올려놓고 HTTP 요청이나 AWS 내의 이벤트가 발생하면 해당 코드가 실행되는 구조이다. 그래서 소스코드만 관리하면 EC2처럼 서버를 직접 관리하지 않아도 되므로 복잡하지 않은 처리에 사용하기에 최적화되어 있다. 가격은 월별 1백만 회, 400,000GB-초의 컴퓨팅 시간까지는 무료이다. 나는 AWS의 대부분 서비스에서 비슷하게 느끼지만, 가격을 보고 실제 사용하면 어느 정도 나올지는 거의 감이 안 오지만 큰 서비스 아니라면 무료로 사용할 수 있어 보인다.
[출저 아웃사이더 블로그]

## Lambda function 작성

* `name` : 인식할 수 있는 함수의 이름을 작성
* `Runtime` : Node.js, Java8, Python 2.7 중에서 선택할 수 있다.
* `Handler` : 이벤트가 발생했을 때 실행할 핸들러를 지정 `index.handler`라고 지정하면 `index.js`파일에 `exporets`로 지정된 `handler` 라는 이름의 함수를 실행하게 된다.
* `Role` : Lambda function에 필요한 Role을 설정한다. S3, 데이터베이스 등 AWS 내의 다른 리서스에 접근이 필요한 경우 IAM에서 Role을 만들어 추가해야한다.
*


### 참고링크
* [AWS Lambda를 이용해서 HTTP API 만들기 1](https://blog.outsider.ne.kr/1205)
*
