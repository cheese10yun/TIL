# Elastic Search

## 설치

> [Install Elasticsearch with Dockeredit Elasticsearch](https://www.elastic.co/guide/en/elasticsearch/reference/current/docker.html) 기반으로 조금 수정해서 docker-compose를 구성했습니다.

```yamlversion: '3.2'
services:
  es01:
    image: docker.elastic.co/elasticsearch/elasticsearch:7.5.1
    container_name: es01
    environment:
      - node.name=es01
      - cluster.name=es-docker-cluster
      - discovery.seed_hosts=es02,es03
      - cluster.initial_master_nodes=es01,es02,es03
      - bootstrap.memory_lock=true
      - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
    ulimits:
      memlock:
        soft: -1
        hard: -1
    volumes:
      - ./volume/data01:/usr/share/elasticsearch/data
    ports:
      - 9200:9200
    networks:
      - elastic

  es02:
    image: docker.elastic.co/elasticsearch/elasticsearch:7.5.1
    container_name: es02
    environment:
      - node.name=es02
      - cluster.name=es-docker-cluster
      - discovery.seed_hosts=es01,es03
      - cluster.initial_master_nodes=es01,es02,es03
      - bootstrap.memory_lock=true
      - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
    ulimits:
      memlock:
        soft: -1
        hard: -1
    volumes:
      - ./volume/data02:/usr/share/elasticsearch/data
    ports:
      - 9201:9201
    networks:
      - elastic

  es03:
    image: docker.elastic.co/elasticsearch/elasticsearch:7.5.1
    container_name: es03
    environment:
      - node.name=es03
      - cluster.name=es-docker-cluster
      - discovery.seed_hosts=es01,es02
      - cluster.initial_master_nodes=es01,es02,es03
      - bootstrap.memory_lock=true
      - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
    ulimits:
      memlock:
        soft: -1
        hard: -1
    volumes:
      - ./volume/data03:/usr/share/elasticsearch/data
    ports:
      - 9202:9202
    networks:
      - elastic

  kib01:
    image: docker.elastic.co/kibana/kibana:7.5.1
    container_name: kib01
    ports:
      - 5601:5601
    environment:
      ELASTICSEARCH_URL: http://es01:9200
      ELASTICSEARCH_HOSTS: http://es01:9200
    networks:
      - elastic

  logstash:
    image: docker.elastic.co/logstash/logstash:7.5.1
    container_name: logstash
    command: logstash -f /usr/share/logstash/pipeline/logstash.conf
    volumes:
      - type: bind
        source: ./logstash/config/logstash.yml
        target: /usr/share/logstash/config/logstash.yml
        read_only: true
      - type: bind
        source: ./logstash/pipeline
        target: /usr/share/logstash/pipeline
        read_only: true
    environment:
      LS_JAVA_OPTS: "-Xmx256m -Xms256m"
    ports:
      - 5044:5044
    networks:
      - elastic
    depends_on:
      - es01
      - es02
      - es03
      - kib01

volumes:
  data01:
    driver: local
  data02:
    driver: local
  data03:
    driver: local
  logstash:
    driver: local

networks:
  elastic:
    driver: bridge
```
es1, es2, es3 Elasticsearch를 클러스터로 연결했고 kib01으로 키바 나를 연결했습니다. logstash 하나 구성했습니다.

```
$ docker-compose -up -d
```
해당 명려어로 실행 시킵니다.


## API 서버 구성

### Dependency
```kotlin
dependencies {
    implementation("org.springframework.boot:spring-boot-starter-web")
	implementation("org.springframework.boot:spring-boot-starter-actuator")
	implementation("net.logstash.logback:logstash-logback-encoder:6.3")
}
```
웹서버에 필요한 디펜던시와, logstash 디펜던시를 추가합니다.


### logback 설정
```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>

  <include resource="org/springframework/boot/logging/logback/base.xml"/>

  <appender name="stash" class="net.logstash.logback.appender.LogstashTcpSocketAppender">
    <destination>127.0.0.1:5044</destination>
    <filter class="ch.qos.logback.classic.filter.ThresholdFilter">
      <level>info</level>
    </filter>
    <encoder class="net.logstash.logback.encoder.LogstashEncoder"/>
  </appender>

  <root level="info">
    <appender-ref ref="stash"/>
  </root>

</configuration>
```

```
<destination>127.0.0.1:5044</destination>
```
위에서 설정한 logstash port를 입력합니다. 본 예제는 port 번호를 5044 설정해서 destination 설정을  5044으로 설정했습니다.

## 구동 스크린샷...?