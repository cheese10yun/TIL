# 도커 입문 자료

## 도커소개

### 도커란?

* 리눅스에서 제공하는 컨테이너를 이용하여 애플리케이션을 묶어서 실행, 배포 할 수 있게 해주는 오픈 소스 SW
* 개발, 테스트, 서비스 서버 등의 다양한 환경을 쉽게 관리 할 수있게 해줌

### 도커의 장점
* 기존 가상 머신에 비해 성능 오버헤드가 적음
* 빠르고 쉬운 애플리케이션 배포
* 이미지 버젼 관리과 쉬움
* 각 컨테이너 사이에 독립적인 동작 환경 제공

### 기존 방식 (가상 머신)
* OS를 가상 머신 마다 중복으로 설치하기 때문에 이밎 크키가 커짐
* 이미지가 커서 네트워크로 배포하는 것이 어려움
* 배포 및 버전 관리가 어려움
	* OS를 포함하고 있어 용량이 크기 떄문에 네트워크를 통해 배포하기가 어려움
	* 이미지의 버전관리 즉 변경사항 추적이 거의 불가능
* 가상 머신은 OS안에 OS를 포함하여 가상화하여 매우 느리고 비효율적임

### 컨테이너(위의 문제를 해결)
* OS를 중복으로 설치하지 않아 이미지 크기가 작음 (배포 편리)
* OS를 가상화하지 않고 직접 호스트의 OS의 자원을 사용하기 때문에 가상머신에 비해 월등히 빠름

### 이미지와 컨테이너

* 서버 구성에 필요한 파일들을 묶어 놓은 것
* 실행 파일, 라이브러리, 설정 파일 등을 포함
* 배포의 단위로 저장소에 올리고 받을 수 있음 (push/pull)
* 컨테이너는 이미지를 실행한 형태
* 실행파일과(이미지) 프로세스(컨테이너)의 관계 정도로 우선 이해하자.


## 도커 설치 (Mac & Linux)

### Mac

* 별거 없다 그냥 홈페이지에서 설치 가능

## 도커 명령어

* 매번 sudo를 입력하는게 귀찮을 경우
```
$ sudo usermod -ag docker ${USER}
$ sudo service docker restart
```

### 이미지 찾기
* 도커 허브에서 공개된 이미지를 검색 가능
* 다양한 리눅스 버전과 rdis, mongoDB 등의 오픈 소스 이미지들을 구할 수 있음
* 이미지 관련 명령어 들은 기본적으로 도커 허브를 사용하도록 설정되어 있음

```
$ docker search redis
```
* 해당 도커 검색

### docker 이미지 설치
```
$ docker pull <이미지 이름>:<태그>
$ docker pull redis:latest
```
* `latest`로 설치할 경우 가장 최신의 버전을 가져온다
* 해당 이미지 이름, 태그를 이용해서 이미지 다운로드

### docker 이미지 확인
```
$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
redis               latest              e4a35914679d        10 days ago         183 MB
```
* 설치된 이미지 확인
* `CREATED`는 설치된 날짜가 아니라, 작성자가 해당 이미지를 올린 날짜이다.

### docker run

```
$ docker run -i -t  --name myredis -d redis
7281c58046adc02cb85fb61ba7e03f508d2c96ea8dfcf201aa73ebb88782b82c
```

* `-i -t` 터미널에서 실행 할때 주는 옵션
* `--name` 컨테이너 이름 지정
* `-d` 데몬 모드(항상 실행 가능??)
* `redis` 실행 시킬 이미지 이름
* `7281...`컨테이너 아이디 출력

### docker 실행중인 컨테이너 목록

```
$ docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS               NAMES
7281c58046ad        redis               "docker-entrypoint..."   5 hours ago         Up 44 seconds       6379/tcp            myredis
```

### docker Start & Stopm

* docker run 이미지를 컨테이너로 만들때 사용
```
$ docker stop myredis
$ docker ps
$ docker ps -a
```
* `--name` 실행시켰던 이름으로 제어
* 실행중인 도커 컨테이너 확인 (myredis stop 시켰으니 출력 안됨)
* `-a` 옵션을 주면 정지된 도커 컨테이너도 표시

### docker 컨테이너 들어가기?
```
$ docker exec -it myredis /bin/bash
```
* `exec`
* `-it` 인터렉티브하게 터미널을 실행한다는 옵션
* `/bin/bash` bash 쉘을 사용한다

### docker 컨테이너 삭제

```
$ docker rm <컨테이이름>
$ docker rmi <컨테이이름>:<태그이름>
```
* 태그 이름을 지정하지 않으면 모든 태그가 삭제된다.
