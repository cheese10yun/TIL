![](https://github.com/cheese10yun/TIL/blob/master/assets/kubernetes-controller.png?raw=true)

## Object
![](https://github.com/cheese10yun/TIL/blob/master/assets/kubernetes-object.png?raw=true)


### Container
![](https://github.com/cheese10yun/TIL/blob/master/assets/kubernetes-container.png?raw=true)

* 컨테이너 안에 Pod이 있다
* 한 컨테이너에서 여러 Pod를 가질 수 있지만 동일한 Pod 내에서 컨테이너는 Port가 동일 할 수 없다.
* 동일한 Pod에서는 Container1 -> Container2로 접근할 때 localhost:8080으로 접근 가능
* Pod가 생성될때 고유한 아이피가 할당된다. 쿠버네티스 클러스 안에서만 해당 아이피로 접근 가능
* Pod의 문제가 있다면 시스템에서 이것을 감지하고 Pod를 삭제하고 다시 만듬, 이때 아이피가 재할당된다.

```yml
apiVersion: v1
kind: Pod
metadata
    name : pod-1
sepc:
    containers:
        -name: container1
        image: tmkube/p8000
        ports:
            - continaerPort: 8000
        -name: container2
        image: tmkube/p8000
        ports:
            -continaerPort: 8080
        
```

### Lebel
* 라벨은 Pod 뿐만 아니라 Object도 사용 가능, 대부분 Pod에사용
* 라벨은 목적에 따라 Object를 분류하고, 그 오브젝트를 따로 연결하기 위함
* 라벨은 key, value가 한 쌍
  * type:web, lo: dev, type:db, lo: dev, type:server, lo: dev
  * type:web, lo: production, type:db, lo: production, type:server, lo: production
* 개발 환경만 보고 싶은 경우 type: dev 확인 가능
* 운영 환경만 접속하고 싶은 경우 type: production 접속 가능

### Node Schedule
* 직접선택: Pod를 만들때 node를 지정할 수 있다. 
* 스캐줄러가 판단: Pod를 만들 때 리소스의 사용량을 지정할 수 있다. 2기가 메모리를 지정, 최대 메모리 3기가 지정
* 메모리: 초과시 Pod 종료 시킴
* CPU: 초과시 request로 낮춤 over시 종료되지 않음


## 참고
* [강의 자료](https://kubetm.github.io/practice/beginner/gettingstarted-kubernetes/)