> [Real MySQL 8.0 (1권) 개발자와 DBA를 위한 MySQL 실전 가이드](http://www.kyobobook.co.kr/product/detailViewKor.laf?mallGb=KOR&ejkGb=KOR&barcode=9791158392703) 정리


# 10 실혱 계획

## 통계정보

MySQL 서버는 5.7 버전까지 테이블과 인덱스에 대한 개괄적인 정보를 가지고 실행 계획을 수립했다. 하지만 이는 테이블 칼럼의 값들이 실제 어떻게 분포돼 있는지에 대한 정보가 없기 때문에 실행 계획의 정확도가 떨어지는 경우가 많았다. 그래서 MySQL 8.0 부터는 인덱스가 되지 않은 칼럼들에 대해서도 데이터 분포도를 수집해서 자장하는 히스토그램 정보가 도입됐다. 히스토그램이 도입됐다고 해서 기존의 테이블이나 인덱스 통계 정보가 필요치 않는 것은 아니다.

### 테이블 및 인덱스 통계 정보

비용 기반 최적화에서 가장 중요한 것은 통계 정보이다. 1억 건의 레코드가 저장된 테이블의 통계 정보가 생신되지 않아서 레코드가 10건 미만인 것처럼 돼 있다면 옵티마이전ㄴ 실제 쿼리를 실행할 때 인덱스 레인지 스캔이 아니라 테이블을 처음부터 끝까지 읽는 풀 테이블 스캔으로 실행해 버릴 수 도 있다.

MySQL은 다른 DBMS 보다 통계 정보의 정확도가 높지 않고 통계 정보의 휘발성이 강했다. 그래서 MySQL 서버에서는 쿼리의 실행 계획을 수립할 때 실제 테이블의 데이터를 일부 분석해서 통계 정보를 보완해서 사용했다. MySQL 5.6 버전부터는 통계 정보의 정확성을 높일 수 있는 방법이 제공되기 시잭했지만 아직도 많은 사용자가 기존 방식을 그대로 사용한다. 여기서는 MySQL 8.0 버전에서 통계 정보 관리가 어떻게 개선됐는지도 함께 살펴 보겠다.

### MySQL 서버의 통계 정보

MySQL 5.6 버전부터는 InnoDB 스토리지 엔진을 사용하는 테이블에 대한 통계 정보를 영구적으로 관리할 수 있게 개선됐다. MySQL 5.5 버전까지는 각 테이블의 통계 정보가 메모리에만 관리되고, SHOW INDEX 명령으로만 테이블의 인덱스 칼럼의 분포도를 볼 수 있었다. 이처럼 통계 정보가 메모리에 관리될 경우 MySQL 서버가 재시작되면 지금까지 수집된 통계정보가 모두 사라진다. MySQL 5.6 버전 부터는 각 테이블의 통계 정보를 mysql 데이터베이스의 innodb_index_stats, innodb_table_stats 테이블로 관리할 수 있게 개선됐다.

```sql
show tables like '%_stats';
```

| Tables\_in\_mysql \(%\_stats\) |
| :--- |
| innodb\_index\_stats |
| innodb\_table\_stats |

MySQL 5.6에서 테이블을 생성할 떄는 STATS_PERSISTENT 옵션을 설정할 수 있는데, 이 설정값에 따라 테이블 단위로 영구적인 통계 정보를 보관할지 말지 결성할 수 있다.

```sql
create table tab_test
(
    fd1 INT,
    fd2 VARCHAR(20),
    PRIMARY KEY (fd1)
)
    ENGINE = InnoDB
    STATS_PERSISTENT = { DEFAULT | 0 | 1}
```

* STATS_PERSISTENT = 0: 테이블의 통계 정보를 MySQL 5.5 이전의 방식대로 관리하며, mysql 데이터베이스의 innodb_index_stats, innodb_table_stats 테이블에 젖아하지 않음
* STATS_PERSISTENT = 1: 
* STATS_PERSISTENT = DEFAULT:

## 실행 계획 확인


## 실행 계획 분석
