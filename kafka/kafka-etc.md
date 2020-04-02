```
## 키기 zookeeper -> server
/usr/local/kafka/bin/zookeeper-server-start.sh /usr/local/kafka/config/zookeeper.properties
/usr/local/kafka/bin/kafka-server-start.sh /usr/local/kafka/config/server.properties

## show topic list
/usr/local/kafka/bin/kafka-topics.sh --list --zookeeper localhost:2181

## create the topic
/usr/local/kafka/bin/kafka-topics.sh --create --bootstrap-server localhost:9092 --replication-factor 1 --partitions 1 --topic test
/usr/local/kafka/bin/kafka-topics.sh --create --bootstrap-server localhost:9092 --replication-factor 1 --partitions 1 --topic numtest

## show the topic
/usr/local/kafka/bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --from-beginning --topic numtest

## show the topic partiton 1
/usr/local/kafka/bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --from-beginning --partition 1 --topic numtest

## delete the topic
/usr/local/kafka/bin/kafka-topics.sh --zookeeper localhost:2181 --delete --topic my_topic

## 끄기 server -> zookeeper
bin/kafka-server-stop.sh config/server.properties
bin/zookeeper-server-stop.sh config/zookeeper.properties

## consumer groups check!
/usr/local/kafka/bin/kafka-consumer-groups.sh  --bootstrap-server localhost:9092 --list

## consumer status and offset check!
/usr/local/kafka/bin/kafka-consumer-groups.sh  --bootstrap-server localhost:9092 --group sr --describe

## consumer group delete
/usr/local/kafka/bin/kafka-consumer-groups.sh --zookeeper localhost:2181 --delete --group <group_name>

## topic leader follower check 
/usr/local/kafka/bin/kafka-topics.sh --zookeeper localhost:2181 --topic my_topic --describe

## server log check
cat /usr/local/bin/kafka/logs/server.log 
```
> 출처 [kafka 자주 사용 명령어 모음](https://data-newbie.tistory.com/241)


토픽 확인 

/usr/local/kafka/bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic yun --from-beginning

/usr/local/kafka/bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic yun