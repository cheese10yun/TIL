# Mysql Replication

## Replication 이란 ?

MySQL에 국한된 것은 아니겠지만, 레플리케이션의 저변에 깔린 사고 방식은 아주 단순하다. **2개의 데이터베이스에 저장되는 데이터가 같고, 이 데이터에 가해진 변경도 같으면 결과도 같다 는 것이다.** 마스터에 일어난 변경을 연속적으로 기록해서 같은 변경을 슬레이브에 전송한다. 슬레이브에서 마스터에 보내온 변경 이력을 연속으로 재생한다. 이렇게 하면 슬레이브의 데이터는 마스터를 따라가게 된다.


## Replication In MySQL

![](https://github.com/cheese10yun/TIL/raw/master/assets/mysql-replication-construct.png)


* 마스터의 변경을 기록하기 위한 바이너리 로그
* 슬레이브에 데이터를 전송하기 위한 마스터 스레드
* 슬레이브에서 데이터를 받아 릴레이 로그에 기록하기 위한 I/O 스레드
* 릴레이 로그에서 데이터를 읽어 재생하기 위한 SQL 스레드

### 바이너리 로그

바이너리 로그는 MySQL 서버에 발생한 모든 변경을 직렬화하여 기록한 파일이다. MySQL 서버가 생성해 보관 중인 바이너로그의 정보는 바이너리 로그 인덱스 파일에 저장한다. 이 때 실제 존재하는 바이너리 로그 파일과 바이너리 로그 인덱스 파일의 내용은 반드시 같아야 한다. 바이너리 로그를 재생한다는 것은 그 SQL문을 순서대로 실행한다는 의미다. 레플리케이션을 위해 바이너리 로그를 사용하는 경우에 한해 statement 기반 레플리케이션(SBR) 이라고 부른다.

SRB의 문제점은 SQL 종류에 따라 실행 결과가 반드시 일치하지 않을 수 있다는 것이다. 대표적으로 UUID() 함수를 사용하는 경우가 대표적인 예다.

이와 같은 비결성 SQL문제를 극복하기 위해서 MySQL 5.1에서 row 기반 포맷, 또는 row 기반 레플리케이션 RBP 모드가 추가되었다 이는 말대로 SQL을 실행한 결과, 변경이 일어난 행의 변경 전후 값을 기록하는 방식이다. 행 단위로 데이터를 기록하기 때문에 SBR에 비해 바이너리 로그 크키가 큰 결점이지만, SQL이 비결정성인지 아닌지에 대해 걱정할 필필요가 없다.

바이너리 로그에는 또 하나의 포맷이 존재한다. Mixed 기반 포맷, 또는 Mixed 기반 레플리케이션이 MBR 이다. 이는 SBR, RBR을 필요에 따라 나누어 사용하는 방식으로, 처음에는 SBR로 기록하다가 비결정성 SQL을 만나면 RBR 방식으로 기록한다. 따라서 비결정성 SQL로 인해 데이터 부정합성이 발생할 걱정이 없다. 두 가지 장점을 모두 취한것이다.

### 레플리케이션을 구성하는 스레드

#### 마스터 스레드
**MySQL의 레플리케이션은 마스터가 서버이고 슬레이브가 클라이언트인 관계다.** 다시말하면, **레플리케이션을 위해 슬레이브가 마스터로 접속하는 형태다. 따라서 마스터는 슬레이브가 로그인하기 위한 계정이 필요하며, REPLICATION SALVE라는 권한을 부여 받아야 한다.**

마스터 스레드의 역할은 단 하나, 바이너리 로그를 읽어 슬레이브에게 전송 하는 것이다. 슬레이브는 단순한 클라이언트 가운데 하나로, 통상의 사용자 스레드와 어떤 차이도 없으면 로그인한 것만으로 슬레이브인지 아닌지 알 도리가 없다. 로그인 후 슬레이브가 바이너리 로그를 송신하도록 지시하고 나면 송신이 시작된다.

#### 슬레이브 I/O 스레드
I/O 스레드는 마스터에 접속해 `COM_BINLOG_DUMP`, `COM_BINLOG_DUMP_GTID` 명령어로 마스터에서 연속적으로 갱신된 내용을 받으며, 받은 데이터를 릴레이 로그라는 로그파일에 보존한다.

#### 슬레이브 SQL 스레드
슬레이브 SQL 스레드는 릴레이 로그에 기록된 갱신 내용을 읽어 들여 슬레이브에서 재생할 때 사용되는 스레드이다.


## MySQL Replication 구축

## Master, Slave Replication 구성

### docker 구성

```yml
# docker-compose.yaml
version: "3"
services:
    db_master:
        image: mysql/mysql-server:5.7
        container_name: master
        hostname: master
        ports:
            - "3306:3306"
        environment:
            MYSQL_ROOT_HOST: '%'
            MYSQL_ROOT_PASSWORD: 'root'
        volumes:
            - ./volumes/db/master/data:/var/lib/mysql
            - ./volumes/db/master/conf.d:/etc/mysql/conf.d
        networks:
            - mybridge
    db_slave:
        image: mysql/mysql-server:5.7
        container_name: slave
        hostname: slave
        ports:
            - "3307:3306"
        environment:
            MYSQL_ROOT_HOST: '%'
            MYSQL_ROOT_PASSWORD: 'root'
        volumes:
            - ./volumes/db/slave/data:/var/lib/mysql
            - ./volumes/db/slave/conf.d:/etc/mysql/conf.d
        networks:
            - mybridge
networks:
    mybridge:
        external: true
```



#### master container 설정

```
$ docker exec -it master /bin/bash
$ mysql -u root -p
$ CREATE USER 'repl'@'%' IDENTIFIED BY 'repl';
$ GRANT REPLICATION SLAVE ON *.* TO 'repl'@'%';
```
* 레플리케이션 계정을 생성하고, 해당 계정에 레플리케이션 권한을 부여

#### slave container 설정

```
$ docker exec -it slave /bin/bash
$ mysql -u root -p
$ reset master;
$ CHANGE MASTER TO MASTER_HOST='slave', \
MASTER_USER='repl', MASTER_PASSWORD='repl', \
MASTER_AUTO_POSITION=1;
$ start slave;
$ show slave status\G
```
* master에서 생성한 레플리케이션으로 슬레이브로 접속함

```
               Slave_IO_State: Waiting for master to send event
                  Master_Host: db001
                  Master_User: repl
                  Master_Port: 3306
                Connect_Retry: 60
              Master_Log_File: mysql-bin.000008
          Read_Master_Log_Pos: 194
               Relay_Log_File: db002-relay-bin.000018
                Relay_Log_Pos: 407
        Relay_Master_Log_File: mysql-bin.000008
             Slave_IO_Running: Yes
            Slave_SQL_Running: Yes
              Replicate_Do_DB:
          Replicate_Ignore_DB:
           Replicate_Do_Table:
       Replicate_Ignore_Table:
      Replicate_Wild_Do_Table:
  Replicate_Wild_Ignore_Table:
                   Last_Errno: 0
                   Last_Error:
                 Skip_Counter: 0
          Exec_Master_Log_Pos: 194
              Relay_Log_Space: 654
              Until_Condition: None
               Until_Log_File:
                Until_Log_Pos: 0
           Master_SSL_Allowed: No
           Master_SSL_CA_File:
           Master_SSL_CA_Path:
              Master_SSL_Cert:
            Master_SSL_Cipher:
               Master_SSL_Key:
        Seconds_Behind_Master: 0
Master_SSL_Verify_Server_Cert: No
                Last_IO_Errno: 0
                Last_IO_Error:
               Last_SQL_Errno: 0
               Last_SQL_Error:
  Replicate_Ignore_Server_Ids:
             Master_Server_Id: 100
                  Master_UUID: d1705987-ab66-11eb-a1f7-0242ac130007
             Master_Info_File: /var/lib/mysql/master.info
                    SQL_Delay: 0
          SQL_Remaining_Delay: NULL
      Slave_SQL_Running_State: Slave has read all relay log; waiting for more updates
           Master_Retry_Count: 86400
                  Master_Bind:
      Last_IO_Error_Timestamp:
     Last_SQL_Error_Timestamp:
               Master_SSL_Crl:
           Master_SSL_Crlpath:
           Retrieved_Gtid_Set: d1705987-ab66-11eb-a1f7-0242ac130007:1-1278
            Executed_Gtid_Set: cd763d8c-ab66-11eb-a1ad-0242ac130003:1-2,
d1705987-ab66-11eb-a1f7-0242ac130007:1-1278
                Auto_Position: 1
         Replicate_Rewrite_DB:
                 Channel_Name:
           Master_TLS_Version:
```
* `Slave_IO_Running: Yes`, `Slave_SQL_Running: Yes` 해당 설정이 정상동작하는 것을 확인할 수 있습니다.


## 참고
* [MySQL 5.7 완벽 분석](http://www.yes24.com/Product/Goods/72270172?)