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

### 엘라스틱서치 데이터 처리

| 관계형 DB | 엘라스틱서치 |
| --------- | ------------ |
| 테이블    | 타입         |
| Row       | 도큐먼트     |
| Column    | 필드         |
| 스키마    | 매핑         |

### 엘라스틱서의 질의 바업
* 커멘드라인 curl 명령어 사용
* postman 응용프로그램 사용
* kibana devtool 사용


## Dev Tools

```
POST customer/type/1
{
  "name": "yun"
}

#! Deprecation: [types removal] Specifying types in document index requests is deprecated, use the typeless endpoints instead (/{index}/_doc/{id}, /{index}/_doc, or /{index}/_create/{id}).
{
  "_index" : "customer",
  "_type" : "type",
  "_id" : "1",
  "_version" : 1,
  "result" : "created",
  "_shards" : {
    "total" : 2,
    "successful" : 1,
    "failed" : 0
  },
  "_seq_no" : 0,
  "_primary_term" : 1
}

```

```
GET customer/type/1
#! Deprecation: [types removal] Specifying types in document get requests is deprecated, use the /{index}/_doc/{id} endpoint instead.
{
  "_index" : "customer",
  "_type" : "type",
  "_id" : "1",
  "_version" : 1,
  "_seq_no" : 0,
  "_primary_term" : 1,
  "found" : true,
  "_source" : {
    "name" : "yun"
  }
}

```


```
DELETE customer/type/1

#! Deprecation: [types removal] Specifying types in document index requests is deprecated, use the /{index}/_doc/{id} endpoint instead.
{
  "_index" : "customer",
  "_type" : "type",
  "_id" : "1",
  "_version" : 2,
  "result" : "deleted",
  "_shards" : {
    "total" : 2,
    "successful" : 1,
    "failed" : 0
  },
  "_seq_no" : 1,
  "_primary_term" : 1
}

```

```
POST tourcompany/customerlist/1
{
  "name": "yun",
  "phone": "010-2333-2323",
  "holday_dst":  "AAADWD",
  "ddeparture_date": "20201/02/02"
}

POST tourcompany/customerlist/2
{
  "name": "kim",
  "phone": "010-3344-2323",
  "holday_dst":  "AAADWD",
  "ddeparture_date": "2021/12/12"
}

POST tourcompany/customerlist/2/_update
{
  "doc": {
    "name": "2222",
  "phone": "010-3344-2323",
  "holday_dst":  "AAADWD",
  "ddeparture_date": "2021/12/12"
  }
  
}

GET tourcompany/customerlist/2
```

##  Batch 프로세스 실행하기

### Batch Processing

```
POST /customer/type1/_bulk?pretty
{"index":{_id:"1"}}
{"name": "John Doe"}
{"index": {"_id":"2"}}
{"name": "Jane Doe"}
```

* bulk API를 사용하여 위의 작업을 일괄 적으로 수행 할 수 있는 기능
* 이 기능은 최대한 적은 네티워크 왕복으로 가능항ㄴ 빨리 여러 작업을 수행할 수 이쓴ㄴ 매으 효율적인 메커니즘 제공
* HTTP 바디 부분 끝에 반드시 엔터티 추가 입력 필요

```
POST /customer/type1/_bulk?pretty
{"update": {"_id":"1"}}
{"doc": {"name":"John Doe become Jane Doe"}}
{"delete": {"_id":"2"}}를 업데이트 한 다으 두 번째 문서 (ID 2)를 일괄 작업에서 삭제
```
* 이 예제는 첫 번째 문서(ID 1)

-----
* Bulk API는 작ㅇ업 중 하나에서 실패 하더라도 전체가 실패는 하지 않음
* 어떤 이유에서 건 하낭의 해동ㅇ이 실패한면, 그 해동의 나머지 행동을 계속해서 처리
* 대량 API가 변환되면 각 액션에 대한 상태가 전송 된 순서대로 제공되므로 특정 액션이 실패했는지 여부를 확인 가능

## 검색 API

### 검색 결과 확인하기

```json
{
  "took": 7,
  "timed_out": false,
  "_shards": {
    "total": 15,
    "successful": 15,
    "skipped": 0,
    "failed": 0
  },
  "hits": {
    "total": {
      "value": 2,
      "relation": "eq"
    },
    "max_score": 2.5739596,
    "hits": [
      {
        "_index": "books",
        "_type": "book",
        "_id": "V_WG6HoBojKvbC6bnmqj",
        "_score": 2.5739596,
        "_source": {
          "title": "The Time Machine",
          "author": "H. G. Wells",
          "category": "Science fiction novel",
          "written": "1895-11-01T05:01:00",
          "pages": 227,
          "sell": 22100000,
          "plot": "The book's protagonist is an English scientist and gentleman inventor living in Richmond, Surrey in Victorian England, and identified by a narrator simply as the Time Traveller. The narrator recounts the Traveller's lecture to his weekly dinner guests that time is simply a fourth dimension, and his demonstration of a tabletop model machine for travelling through it. He reveals that he has built a machine capable of carrying a person, and returns at dinner the following week to recount a remarkable tale, becoming the new narrator."
        }
      },
      {
        "_index": "magazines",
        "_type": "magazine",
        "_id": "XfWH6HoBojKvbC6bXWqO",
        "_score": 1.2039728,
        "_source": {
          "title": "Time",
          "company": "Time Inc.",
          "category": "News magazine",
          "issue": "2014-03-01T00:00:00"
        }
      }
    ]
  }
}
```

| 항목       | 설명                                               |
| ---------- | -------------------------------------------------- |
| took       | Elasticsearch가 검색한 실행하는 데 걸리는 시간(ms) |
| timed_out  | 검색 시간이 초과되었는지 여부를 알림               |
| shards     | 검색된 파편의 수와 성공 / 실패한 파편의 수를 알림  |
| hits       | 검색 결과                                          |
| hits.total | 검색 조건과 일치하는 총 문서 수                    |
| hits.hits  | 검색 결과의 실제 배열(기본 값은 처음 10개)         |
| hits.sort  | 결과 정렬 키(점순수 정렬시 누락)                   |


```
DELETE tourcompany

POST tourcompany/customerlist/_bulk
{"index":{"_id":"1"}}
{"name":"Alfred","phone":"010-1234-5678","holiday_dest":"Disneyland","departure_date":"2017/01/20"}
{"index":{"_id":"2"}}
{"name":"Huey","phone":"010-2222-4444","holiday_dest":"Disneyland","departure_date":"2017/01/20"}
{"index":{"_id":"3"}}
{"name":"Naomi","phone":"010-3333-5555","holiday_dest":"Hawaii","departure_date":"2017/01/10"}
{"index":{"_id":"4"}}
{"name":"Andra","phone":"010-6666-7777","holiday_dest":"Borabora","departure_date":"2017/01/11"}
{"index":{"_id":"5"}}
{"name":"Paul","phone":"010-9999-8888","holiday_dest":"Hawaii","departure_date":"2017/01/10"}
{"index":{"_id":"6"}}
{"name":"Colin","phone":"010-5555-4444","holiday_dest":"Venice","departure_date":"2017/01/16"}


GET tourcompany/customerlist/_search?q="010-3333-5555"

GET tourcompany/customerlist/_search?q="Disneyland"&_source=phone,holiday_dest

GET tourcompany/customerlist/_search?q="2017/01/10" or "2021/01/11"&sort=name.keyword
```