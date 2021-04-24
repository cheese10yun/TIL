> [따라하며 배우는 MySQL on Docker](https://www.inflearn.com/course/mysql-docker/dashboard) 정리

## Docker Mysql Container 실행

```
$ docker run -i -t --name db--1 -e MYSQL_RROT_PASSWORD="root" - d percona:5.7.30
```
* `-i -t`: container에 shell로 접속해서 사용을 하기 위한 옵션
* `--name`: container 이름
* `-e`: 환경변수 셋팅
* `-d`: backgound mode로 container 실행

## 외부에서 Mysql Container 접속

```
docker run -i -t --name db001 -p 3306:3306 -e MYSQL_RROT_PASSWORD="root" - d 
```

## Volume 설정

```
$ mkdir -p /db/db001/data
$ chmod 777 /db /db/db001 /db/db001/data
$ docker run -i -t --name db001 -p 3306:3306 -v /db/db001/data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD="ROOT" -d percona:5.7.30
```

## Master, Slave Replication 구성
