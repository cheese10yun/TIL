# Grafana

# Docker 


```
docker run
새로운 컨테이너에서 해당 명령어를 실행
- d, --detach: 백그라운드에서 컨테이너 실행 및 컨테이너 ID 출력
-- name: 컨테이너 이름 할당
-- network: 컨테이너를 네트워크에 연결
-v, --volume: 컨테이너 볼륨을 호스트 볼룸에 바인딩
```


```
docker ps
컨테이너 출력 목록
-a, --all 옵션을 주지 않을시, 실행중인 컨테이너만 ㅊㄹ력

docker logs
컨테이너 로그 출력
-f, --follow 컨테이너 로그 추적
```

```
docker restar
하나 이상의 컨테이너 재시작

docker rm
하나 이상의 커넽이너 제거
-f, --force 실행중인 컨테이너 강제 제거  

```

## Prometheus 설치 옵션

```
--config.file
설정파일 경로 설정
--storage.tsdb.path
메트릭 저장소 경로
--web.enalbe-lifecylce
http 통신을 위한 prometheus reload 및 shutdown 활성화
--strage.tsdb.retention.time
얼마나 오래동안 데이터를 유지할지
--log.level
저장된 단계 이상으로 로그를 보임
```