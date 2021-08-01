> [IT인을 위한 ELK 통합로그시스템 구축과 활용](https://www.inflearn.com/course/ELK-통합로그시스템-IT보안/dashboard)


# 엘라스틱서치 이해화 실습

## 엘라스틱서치 CRUD

### REST API
* 노드와 통신 하는 방법
* Elasticsearch 클러스터와 상호 작용하는 데 사용할 수 있는 매우 포괄적인 강력한 REST API 제공
* API로 수행 할 수 있는 몇 가지 작업
  * 클러스터, 노드 및 색인 상태, 상태 및 통계 확인
  * 클러스터, 노드 및 색인 데이터 및 메타 데이터 관리
  * CRUD 및 인덱스에 대한 검색 작업 수행
  * 페이징, 정렬, 필터링, 스크립팅, 집계 및 기타 여러 고급 검색 작업 실행


### 클러스터 상태
* 클러스타가 어떻게 진행되고 있는지 기본적인 확인
* HTTP/REST 호출을 수행 할 수 있는 모든 도구를 사용 가능
* 클러스터 상태를 확인 하기위해 _cat API를 사용
  * 녹색: 모든 것이 좋음 (클러스터가 완전히 작동함)
  * 노란색: 모든 데이터를 사용할 수 있지만 일부 복제본은 아직 할당되지 않음
  * 빨간색: 어떤 이유로든 일부 데이터를 사용할 수 없음


```
GET /_cat/nodes?v

?v 있는 경우
ip           heap.percent ram.percent cpu load_1m load_5m load_15m node.role master name
192.168.0.10           58          40   0    0.01    0.05     0.03 dilm      *      localhost

?v 없는 경우
192.168.0.10           58          40   0    0.01    0.05     0.03 dilm      *      localhost
```
* v = 자세히 보기, 필드명 출력

```
GET /_cat/indices?v

health status index                              uuid                   pri rep docs.count docs.deleted store.size pri.store.size
green  open   .apm-agent-configuration           V7tkTCr-QB23K8jO5AZm6Q   1   0          0            0       283b           283b
yellow open   logstash-2021.07.30                7BHZsoq3SmmTqGwtY632tw   1   1          1            0      5.4kb          5.4kb
yellow open   apm-7.6.2-transaction-000001       eHZMOOZyQQWcaMDiPUCrdA   1   1       5885            0      2.2mb          2.2mb
yellow open   filebeat-7.6.2-2021.07.27-000001   LFI7CuccQI2Oj54j9FawTA   1   1       9632            0      1.6mb          1.6mb
yellow open   apm-7.6.2-profile-000001           wsoQw08xSbCD-fXdvR3c5A   1   1          0            0       283b           283b
green  open   .kibana_1                          nsrRuUKOT3yKnBW7_4fS8w   1   0       3125           80      2.8mb          2.8mb
yellow open   apm-7.6.2-metric-000001            sSlJw5dZR_yZeee-KESYEQ   1   1     120259            0     15.6mb         15.6mb
yellow open   apm-7.6.2-span-000001              iqCwrDzfTMWtPYIjwusCaw   1   1        571            0      229kb          229kb
yellow open   filebeat-7.13.4-2021.07.27-000001  c0Yl5TBwSWWeoLlX0rGI4A   1   1          0            0       283b           283b
yellow open   books                              jJjapZV6SDCoJ8s8-E819Q   1   1         15            0     32.4kb         32.4kb
yellow open   apm-7.6.2-error-000001             VPLyegIbQeiOLPyAO6LFQA   1   1          6            0     50.1kb         50.1kb
green  open   .kibana_task_manager_1             QDafBKG1RE-gOb9k60rdjg   1   0          2            1     32.5kb         32.5kb
green  open   ilm-history-1-000001               QHCL2hEkSnq_A-SWNgYbLw   1   0        104            0     92.8kb         92.8kb
yellow open   metricbeat-7.6.2-2021.07.27-000001 IbuKDJOJTZKVYtgcbM0iqw   1   1       1085            0        1mb            1mb
yellow open   magazines                          _FXF1VkdTm6-IvjaRYTaUQ   1   1          4            0      5.5kb          5.5kb
yellow open   apm-7.6.2-onboarding-2021.07.27    78Hj-AEXQXy5u85-botxHw   1   1          1            0      6.2kb          6.2kb
yellow open   logstash-2021.07.28                4qhdwBoYT9-O9HRW0AKFag   1   1          1            0      5.3kb          5.3kb

```