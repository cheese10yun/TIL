# Grafana & Prometheus

## Prometheus Docker

```
docker run \
-d \
--name=prometheus \
--network=host \
-v /prometheus/config:/etc/prometheus  \
-v /prometheus/config:/data \
prom/prometheus:v2.29.2 \
--config.file=/etc/prometheus/prometheus.yml \
--storage.tsdb.path=/data --web.enable-lifecycle \
--storage.tsdb.retention.time=10d
```

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

## Prometheus 설정

### global

````
global: 전역으로 사용되는 설정 값
scrape_internal: 메트릭 수집 주기
scrape_timeout: 수집 요청 시 timeout 
evaluatioj_interval: rule_files에 명시된 규칙 확인 주기
````

### alerting

````
alerting: Alertmanager에 연관된 설정 명시부
alertmanagers: 알람을 받을 Alertmanager 서버 관련 설정
follow_redirects: 수집 시, 리다이렉션을 따를지 여부
scheme: 요청 시 사용될 프로토콜(http)
timeout: Alertmanager가 알림을 받을 시에 timeout
static_configs: Alertmanager로 사용될 대상 서버 설정
targets: Alertmanager로 사용될 대상 서버 리스트
rule_files: 메트릭 알람 조건 
````

### scrape_configs

```
scrape_configs: 수집할 방법 및 대상을 설정하는 부분
job_name: 수집될 메트릭에 할당될 그룹 이름
scrape_interval: 메트릭스 수집 주기 (global 설정)
scrape_path: 메트릭을 가져올 요청 경로 (/metrics)
scheme: 요청 시 사용될 프로토컬(http)
follow_redirects: 수집 시, 리다이렉션을 따를지 여부(tue)
static_configs: 수집될 대상 서버 설정
targets: 대상 서버 리스트
```

## Exporter

Exporters는 특정 메트릭을 수집해서 외부로 노출시키는 애플리케이션

## 서비스 디스커버리

서비스 디스커버리는 타겟 서버에 대한 정보를 가지고 오기 위한 설정, 리벨을 통해 타겟 서버 관리

## 메트릭 종류

| 종류 | 설명 |
|---------|--|
| Counter | 누적 개수 |
| Gauge | 현재 상태 |
| Histogram | 특정 기간 동안의 집계 |
| Summary | Histogram과 유사, Quantile |


## 데이터 유형


| 종류 | 설명 |
|---------|--|
| Instant vector | 동 시간대 샘플 집합 |
| Range vector | 특정 시간 범위의 샘플 집합 |
| Scalar | 숫자 |
| String | 문자 |


