> [아파치 카프카 애플리케이션 프로그래밍 with 자바](http://www.yes24.com/Product/Goods/99122569)을 보고 정리한 글입니다.


# 2 카프카 빠르게 시작해보기

## 주키퍼 카프카 브로커 실행

```
$ wget https://mirror.navercorp.com/apache/kafka/2.8.0/kafka_2.12-2.8.0.tgz
$ tar zxf kafka_2.12-2.8.0.tgz
$ cd kafka_2.12-2.8.0
```

### 카프카브로커 힙 메모리 설정
칼프카 브로커를 실행하기 위해서는 힙 메모리 설정이 필요하다. **카프카 브로커는 레코드의 내용을 페이지 캐시로 시스템 메모리를 사용하고 나머지 객체들은 힙 메모리에 저장하여 사용 한다는 특징이 있다.** 이러한 특징으로 카프카 브로커를 운영할 때 힙 메모리를 5GB 이상으로 설정하지 않는 것이 일반적이다. 실습이나 테스트 목적이라면 힙 메모리를 작게 설정해도 무관하다. **카프카 패키지의 힙 메로리는 카프카 브로커는 1G, 주피퍼는 512MB로 기본 설정되어 있다.**


### 카프카 브로커 실행 옵션 설정

confg 폴더에 있는 server.properties 파일에 있는 카프카 브로커가 클러스터 운영에 필요한 옵션을을 지정할 수 있다. 실습용 카프카 브로커를 실행할 것이므로 `advertiesd.listener`만 설정하면 된다. `advertiesd.listener`는 카프카 클라이언트 또는 커맨드 라인툴을 브로커와 연결할 때 사용 된다. 현재 접속하고 있는 인스턴스의 퍼블릭 IP와 카프카 기본 포트 9092를 `PLAINTEXT://`와 함께 붙여 넣고 `advertiesd.listener`를 주석에서 해제한다. 이렇게 설정한 옵션은 카프카 브로커를 실핼할 때 `kafka-server.start.sh` 명령어와 함께 지정할 수 있다. 이미 실행되고 있는 카프카 브로커의 설정을 변경하고 싶다면 브로커를 재시작해야 하므러 신중히 결정해야 한다.



```properties


############################# Server Basics #############################

# The id of the broker. This must be set to a unique integer for each broker.
broker.id=0 # (1)

############################# Socket Server Settings #############################

# The address the socket server listens on. It will get the value returned from 
# java.net.InetAddress.getCanonicalHostName() if not configured.
#   FORMAT:
#     listeners = listener_name://host_name:port
#   EXAMPLE:
#     listeners = PLAINTEXT://your.host.name:9092
#listeners=PLAINTEXT://:9092 # (2)

# Hostname and port the broker will advertise to producers and consumers. If not set, 
# it uses the value for "listeners" if configured.  Otherwise, it will use the value
# returned from java.net.InetAddress.getCanonicalHostName().
#advertised.listeners=PLAINTEXT://your.host.name:9092 # (3)

# Maps listener names to security protocols, the default is for them to be the same. See the config documentation for more details
#listener.security.protocol.map=PLAINTEXT:PLAINTEXT,SSL:SSL,SASL_PLAINTEXT:SASL_PLAINTEXT,SASL_SSL:SASL_SSL # (4)

# The number of threads that the server uses for receiving requests from the network and sending responses to the network
num.network.threads=3 # (5)

# The number of threads that the server uses for processing requests, which may include disk I/O
num.io.threads=8 # (6)

# The send buffer (SO_SNDBUF) used by the socket server
socket.send.buffer.bytes=102400

# The receive buffer (SO_RCVBUF) used by the socket server
socket.receive.buffer.bytes=102400

# The maximum size of a request that the socket server will accept (protection against OOM)
socket.request.max.bytes=104857600


############################# Log Basics #############################

# A comma separated list of directories under which to store log files
log.dirs=/tmp/kafka-logs # (7)

# The default number of log partitions per topic. More partitions allow greater
# parallelism for consumption, but this will also result in more files across
# the brokers.
num.partitions=1 # (8)

# The number of threads per data directory to be used for log recovery at startup and flushing at shutdown.
# This value is recommended to be increased for installations with data dirs located in RAID array.
num.recovery.threads.per.data.dir=1

############################# Internal Topic Settings  #############################
# The replication factor for the group metadata internal topics "__consumer_offsets" and "__transaction_state"
# For anything other than development testing, a value greater than 1 is recommended to ensure availability such as 3.
offsets.topic.replication.factor=1
transaction.state.log.replication.factor=1
transaction.state.log.min.isr=1

############################# Log Flush Policy #############################

# Messages are immediately written to the filesystem but by default we only fsync() to sync
# the OS cache lazily. The following configurations control the flush of data to disk.
# There are a few important trade-offs here:
#    1. Durability: Unflushed data may be lost if you are not using replication.
#    2. Latency: Very large flush intervals may lead to latency spikes when the flush does occur as there will be a lot of data to flush.
#    3. Throughput: The flush is generally the most expensive operation, and a small flush interval may lead to excessive seeks.
# The settings below allow one to configure the flush policy to flush data after a period of time or
# every N messages (or both). This can be done globally and overridden on a per-topic basis.

# The number of messages to accept before forcing a flush of data to disk
#log.flush.interval.messages=10000

# The maximum amount of time a message can sit in a log before we force a flush
#log.flush.interval.ms=1000

############################# Log Retention Policy #############################

# The following configurations control the disposal of log segments. The policy can
# be set to delete segments after a period of time, or after a given size has accumulated.
# A segment will be deleted whenever *either* of these criteria are met. Deletion always happens
# from the end of the log.

# The minimum age of a log file to be eligible for deletion due to age
log.retention.hours=168 # (9)

# A size-based retention policy for logs. Segments are pruned from the log unless the remaining
# segments drop below log.retention.bytes. Functions independently of log.retention.hours.
#log.retention.bytes=1073741824 # (10)

# The maximum size of a log segment file. When this size is reached a new log segment will be created.
log.segment.bytes=1073741824

# The interval at which log segments are checked to see if they can be deleted according
# to the retention policies
log.retention.check.interval.ms=300000 # (11)

############################# Zookeeper #############################

# Zookeeper connection string (see zookeeper docs for details).
# This is a comma separated host:port pairs, each corresponding to a zk
# server. e.g. "127.0.0.1:3000,127.0.0.1:3001,127.0.0.1:3002".
# You can also append an optional chroot string to the urls to specify the
# root directory for all kafka znodes.
zookeeper.connect=localhost:2181 # (12)

# Timeout in ms for connecting to zookeeper
zookeeper.connection.timeout.ms=18000 # (12)
...
```

* (1) `broker.id=0`
  * 실행하는 카프카 브로커의 번호를 적는다. 클러스터를 구축할 때 브로커들을 구분하기 위하 단 하나뿐인 번호로 설정 해야한다. 다른 카프카 브로커와 동일한 id를 가질 경우 비정상적인 동작이 발생할 수 있다.
* (2) `listeners=PLAINTEXT://:9092`
  * 카프카 브로커가 통신을 하기 위해 열어둘 인터페이스 IP, port, 프로토컬을 설정할 수 있다. 따로 설정하지 않으면 모든 IP와 port에 접속할 수 있다.
* (3) `advertised.listeners=PLAINTEXT://your.host.name:9092`
  * 카프카 클라이언트 또는 카프카 커맨드 라인 툴에서 접속할 때 사용하는 IP, port 정보다.
* (4) `SASL_PLAINTEXT:SASL_PLAINTEXT,SASL_SSL:SASL_SSL`
  * SASL_SSL, SASL_PLAIN 보안 설정 시 프로토콜을 매핑을 위한 설정이다.
* (5) `num.network.threads=3`
  * 네트워크를 통한 처리를할 때 사용할 네트워크 스레드 개수 설정이다.
* (6) `num.io.threads=8`
  * 카프카 브로커 내부에서 사용할 스레드 개수를 지정할 수 있다.
* (7) `log.dirs=/tmp/kafka-logs`
  * 통신을 통해 가져온 데이터를 파일로 저장할 디렉토리 위치다.
  * 디렉토리가 생성되어 있지 않으면 오류가 발생할 수 있으므로 브로커 실행 전에 디렉토리 생성 여부를 확인 한다.
* (8) `num.partitions=1`
  * 파티션 개수를 명시하지 않고 토픽을 생성할 때 기본 설정이되는 파티션 개수다.
  * 파티션 개수가 많아지면 병렬처리 데이터량이 늘어난다.
* (9) `log.retention.hours=168`
  * 카프카 브로커가 저장한 파일이나 삭제되기까지 걸리는 시간을 설정할 수 있다.
  * 가장 작은 단위를 기준으로 하므로 `log.retention.hours` 보다는 `log.retention.ms` 값을 설정하여 운영하는 것을 권장한다.
  * `log.retention.ms` 값을 -1로 설정하면 파일은 영원히 사라지지 않는다.
* (10) `log.segment.bytes=1073741824`
  * 카프카 브로커가 지정할 파일의 최대 크기를 지정한다. 데이터양이 많아 이 크기를 채우게 되면 새로운 파일이 생성된다.
* (11) `log.retention.check.interval.ms=300000`
  * 카프카 브로커가 지정한 파일을 삭제하기 위해 체크하는 간격을 지정할 수 있다.
* (12) `zookeeper.connect=localhost:2181`
  * 카프카 브로커와 연동할 주키퍼의 IP, port를 지정한다.
  * 해당 환경에서는 주키퍼와 카프카 브로커를 동시에 실행하므로 `localhost:2181`을 사용
* (13) `zookeeper.connection.timeout.ms=18000`
  * 주키퍼의 세션 타임아웃 시간을 지정한다.


### 주키퍼 실행

카프카 바이너리가 포함된 폴더에는 브로커와 같이 실핼할 주키퍼가 준비되어 있다. 분산 코디네이션 서비스를 제공하는 주키퍼는 카프카의 클러스터 설정 리더 정보, 컨트롤러 정보를 담고 있어 카프카를 실행하는 데 필요한 필수 애플리케이션이다. 주키퍼를 상용환경에선느 안전하게 운영하기 위해서는 3대 이상의 서버로 구성하여 사용하지만 실습환경에서는 동일한 서버에 카프카와 동시에 1대만 실행 시켜 사용할 수 있다. 1대만 실행하는 주키퍼를 `Quick-and-diry single-node`라고 부른다. 즉, 주키퍼 1대만 실행하여 사용하면 안되고, 테스트 목적으로만 사용해야한다. `-daemon` 옵션과 주키퍼 설정 경로인 `cofng/zookeeper.properties`와 함께 주키퍼 시작 스크립트인 `bin/zookeeper-server.start.sh`를 실행하면 주키퍼를 백그라운드에서 실행할 수 있다. 주키퍼가 정상적으로 실행되었는지는 jps 명령어로 확인할 수 있다. jps는 JVM 프로세스 상태를 보든 도구로서 JVM 위에서 동작하는 주키퍼의 프로세스를 확인할 수 있다. `-m` 옵션과 함께 사용하면 main 메서드에 전달된 인자를 확인할 수 있고, `-v` 옵션을 사용하면 JVM에 전달된 인자 (힙 메모리 설정, log4j 설정 등)를 함께 확인할 수있다. 만약 주키퍼 시작 스크립트를 넣었는데도 불과하고 정상적으로 실행되지 않는다면 `-daemon` 옵션을 제외하고 실행하면 정확한 로그를 확인할 수 있다.

```

$ cd kafka_2.12-2.8.0
$ bin/zookeeper-server-start.sh -daemon config/zookeeper.properties

2672 QuorumPeerMain /usr/local/zookeeper/conf/zoo.cfg -Dzookeeper.log.dir=. -Dzookeeper.root.logger=INFO,CONSOLE -Dcom.sun.management.jmxremote -Dcom.sun.management.jmxremote.local.only=false
...
```


### 카프카 브로커 실행 및 로그 확인

`-daemon` 옵션과 함께 카프카 브로커를 백그라운드 모드로 실행한다. `kafka-server-start.sh` 명령어를 통해 카프카 브로커를 실행한뒤 `jps` 명령어를 통해 주키퍼와 브로커 프로세스의 동작 여부를 알 수 있다. 그리고 tail 명령어를 통해 로그를 확인하여 브로커 프로세스 동작 여부를 알 수 있다. 그리고 tail 명령어를 통해 로그를 확인하여 카프카 브로커가 정상동작하는지 확인할 수있다. 카프키 브로커의 로그를 확인하는 것은 매주 중요한데, 카프카 클라이언트를 개발할 때뿐만 아니라 카프카 클러스터를 운영할 때 이슈ㅜ가 발생할 경우 모두 카프카 브로커에 로그가 남기 때문이다. 카프카 클라이언트를 개발하여 연동할 때 정상적으로 연결되지 않거나 데이터가 전송되지 않는다면 카프카 브로커가 실행되고 이쓴ㄴ 서버로 접속하여 로그를 확인하면 더욱 빠르게 문제를 해결할 수 있다.

```
$ cd kafka_2.12-2.8.0
$ bin/kafka-server-start.sh -daemon config/server.properties
$ jps -m
```


### 로컬 컴퓨터에서 카프카와 통신 확인

```
$ wget https://mirror.navercorp.com/apache/kafka/2.8.0/kafka-2.8.0-src.tgz
$ tar xzf kafka-2.8.0-src.tgz
$ cd kafka-2.8.0-src
$ ls bin
connect-distributed.sh        kafka-consumer-perf-test.sh          kafka-producer-perf-test.sh         kafka-verifiable-producer.sh
connect-mirror-maker.sh       kafka-delegation-tokens.sh           kafka-reassign-partitions.sh        trogdor.sh
connect-standalone.sh         kafka-delete-records.sh              kafka-replica-verification.sh       windows
kafka-acls.sh                 kafka-dump-log.sh                    kafka-run-class.sh                  zookeeper-security-migration.sh
kafka-broker-api-versions.sh  kafka-features.sh                    kafka-server-start.sh               zookeeper-server-start.sh
kafka-cluster.sh              kafka-leader-election.sh             kafka-server-stop.sh                zookeeper-server-stop.sh
kafka-configs.sh              kafka-log-dirs.sh                    kafka-storage.sh                    zookeeper-shell.sh
kafka-console-consumer.sh     kafka-metadata-shell.sh              kafka-streams-application-reset.sh
kafka-console-producer.sh     kafka-mirror-maker.sh                kafka-topics.sh
kafka-consumer-groups.sh      kafka-preferred-replica-election.sh  kafka-verifiable-consumer.sh
$ bin/kafka-broker-api-versions.sh --bootstrap-server 192.168.0.10:9092

localhost:9092 (id: 0 rack: null) -> (
	Produce(0): 0 to 9 [usable: 9],
	Fetch(1): 0 to 12 [usable: 12],
	ListOffsets(2): 0 to 6 [usable: 6],
	Metadata(3): 0 to 11 [usable: 11],
	LeaderAndIsr(4): 0 to 5 [usable: 5],
	StopReplica(5): 0 to 3 [usable: 3],
	UpdateMetadata(6): 0 to 7 [usable: 7],
	ControlledShutdown(7): 0 to 3 [usable: 3],
	OffsetCommit(8): 0 to 8 [usable: 8],
	OffsetFetch(9): 0 to 7 [usable: 7],
	FindCoordinator(10): 0 to 3 [usable: 3],
	JoinGroup(11): 0 to 7 [usable: 7],
	Heartbeat(12): 0 to 4 [usable: 4],
	LeaveGroup(13): 0 to 4 [usable: 4],
	SyncGroup(14): 0 to 5 [usable: 5],
	DescribeGroups(15): 0 to 5 [usable: 5],
	ListGroups(16): 0 to 4 [usable: 4],
	SaslHandshake(17): 0 to 1 [usable: 1],
	ApiVersions(18): 0 to 3 [usable: 3],
	CreateTopics(19): 0 to 7 [usable: 7],
	DeleteTopics(20): 0 to 6 [usable: 6],
	DeleteRecords(21): 0 to 2 [usable: 2],
	InitProducerId(22): 0 to 4 [usable: 4],
	OffsetForLeaderEpoch(23): 0 to 4 [usable: 4],
	AddPartitionsToTxn(24): 0 to 3 [usable: 3],
	AddOffsetsToTxn(25): 0 to 3 [usable: 3],
	EndTxn(26): 0 to 3 [usable: 3],
	WriteTxnMarkers(27): 0 to 1 [usable: 1],
	TxnOffsetCommit(28): 0 to 3 [usable: 3],
	DescribeAcls(29): 0 to 2 [usable: 2],
	CreateAcls(30): 0 to 2 [usable: 2],
	DeleteAcls(31): 0 to 2 [usable: 2],
	DescribeConfigs(32): 0 to 4 [usable: 4],
	AlterConfigs(33): 0 to 2 [usable: 2],
	AlterReplicaLogDirs(34): 0 to 2 [usable: 2],
	DescribeLogDirs(35): 0 to 2 [usable: 2],
	SaslAuthenticate(36): 0 to 2 [usable: 2],
	CreatePartitions(37): 0 to 3 [usable: 3],
	CreateDelegationToken(38): 0 to 2 [usable: 2],
	RenewDelegationToken(39): 0 to 2 [usable: 2],
	ExpireDelegationToken(40): 0 to 2 [usable: 2],
	DescribeDelegationToken(41): 0 to 2 [usable: 2],
	DeleteGroups(42): 0 to 2 [usable: 2],
	ElectLeaders(43): 0 to 2 [usable: 2],
	IncrementalAlterConfigs(44): 0 to 1 [usable: 1],
	AlterPartitionReassignments(45): 0 [usable: 0],
	ListPartitionReassignments(46): 0 [usable: 0],
	OffsetDelete(47): 0 [usable: 0],
	DescribeClientQuotas(48): 0 to 1 [usable: 1],
	AlterClientQuotas(49): 0 to 1 [usable: 1],
	DescribeUserScramCredentials(50): 0 [usable: 0],
	AlterUserScramCredentials(51): 0 [usable: 0],
	AlterIsr(56): 0 [usable: 0],
	UpdateFeatures(57): 0 [usable: 0],
	DescribeCluster(60): 0 [usable: 0],
	DescribeProducers(61): 0 [usable: 0]
)
```

`kafka-broker-api-versions.sh` 명령엉와 함께 --boorstrap-server에 인스턴스 IP와 9092 포트를 넣으면 원격으로 카프카 버전과 brokder.id, rack 정보, 각종 카프카 브로커 옵션들을 확인할 수 있다.