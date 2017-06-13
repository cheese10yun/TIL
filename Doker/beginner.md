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
$ sudo usermod -a -G docker ${USER}
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

## 컨테이너 개념정리

* 이미지 : 설계도
* 컨테어너 : 이미지(설계도)를 기반으로 제품 생성

## 명령어 개념 정리

* docker start my-container : 컨테이너 실행
* docker exec -it my-container myjob.sh : myjob의 스크립트 파일 실행 (프로세스 실행)
* docker logs -ft my-container : `-tf` 옵션을 주면 실행 이전까지 로그 출력됨

## 컨테이너 하드웨어 자원 제어
### CPU 사용량 제어
```
$ docker run -ti --c 512 ubuntu:16.03/bin/bash
```

* `--c` 또는 `--cpu-share` 기본값은 1024이고 100%를 의미함 512값인 경우는 50%
* 1024

### Memory Share constraint

```
$ docker run -ti --m 300M ubuntu:16.04 /bin/bash
```

* `--m` 또는 `--memory` : 메모리 제한 값 최소 `4m`
* 별도의 설정이 없다면 `swap`도 `300m`, swap + memory = `600m`
* 단위: `b, k, m, g`

### Swap Memory 제어

```
$ docker run -ti --m 300M --memory-swap 1G ubuntu:16.04 /bin/bash
```
* `--memory-swap 1G` swap 메모리 설정
* `memory` + `swap` = 1.3G

### 깨긋한 도커 관리를 위한 꿀팁

```
$ docker kill $(docker ps -q)
$ docker rm $(docker ps -a -q)
$ docker ps -a | grep 'weeks ago'| awk '{print $1}'|xargs docker rm
```

* 실행 중인 모든 컨테이너를 죽인다. `-q` 종료 옵션
* 정지된 것을 포함해서, 모든 컨테이너를 삭제한다. `-a -q` a옵션은 정지된 것도 포함, q 옵션으로 제거
* 오래된 컨테이너를 삭제하다.

```
$ docker rm -v $(docker ps -a -q -f status=exited)

$ docker rmi $(docker images -q -f dangling=true)
```

$ docker volume rm $(docker volume ls -q dangling=true)

* 정지된 컨테이너를 삭제한다 : `status=exited`를 통해서 정지된 도커 아이디 값을 가져오고 그값을 `rm -v` 옵션으로 컨테이너를 정지시킨다.
* 불필요한 이미지를 제거한다 : `dangling=true` 불필요한 이미지를 가져온다. `rmi` 옵션으로 이미지를 제거한다.
* 불필요한 볼륨을 제거한다 : `dangling=true` 옵션으로 불필요 볼륨을 가져온다. `volume rm`으로  볼륨제거


### 이미지나 컨테이너를 파일로 관리
* 도커에서 `push, pull`로 명령어로 저장소를 통해서 배포가 가능하나 별도의 저장소를 구성하기 어렵거나, 방화벽이나 다양한 이유로 도커 허브를 사용하기 어려운 경우
* 컨테이너 이미지를 파일로 저장하고 불러오는 기능을 제공한다.

### Load Image
```
$ docker load < my_image.tar.gz
```
* 해당 이미지를 불러온다.

### Save Image

```
$ docker save my_image:my_tag > my_image.tar.gz
```
* 해당 이미지 저장

### Load Container

```
$ cat my-container.tar.gz | docker import - my-container:my_tag
```

* 해당 컨테이너 불러옴


### Save Container
```
$ docker export my-container > my-container.tar.gz
```
* 해당 컨테이너 저장

### Read-only Container

```
docker run --read-only
```
* 항상 읽기 전용으로만 실행된다.

## Docker Monitoring 1

```
$ docker stats <container>
```
* 하나의 컨테이너의 자원 사용량 모니터링

```
$ docker stats $(docker ps -q)
```
* 모든 컨테이너의 자원 사용량 모니터링(컨테이너 ID로 정렬)
* `docker ps -q` 옵션으로 현재 실행중인 컨테이너 목록가져옴
* `stats` 옵션으로 컨테이너 자원 사용량 모니터링

## Docker Monitoring 2

```
$ docker stats $(docker ps --format '{{.Names}}')
```
* 이름 순으로 정렬하여 모니터링 하기
* `--format '{{.Names}}'` Names의 필드 값을 가져와서 정렬
```
$ docker ps -a -f ancestor=ubuntu
```

* `ubuntu` 이미지로 만들어진 컨테이너 찾기
* `ancestor` 옵션으로 찾고 싶은 컨테이너 이름 입력
* `-a` 옵션은 실행중인 것과 정지된것 모든것을 가져옴

## 이미지를 빌드하는 두가지 방법

### docker commit 명령어
* 간편하나 실수하 가능성이 높음
* 과정을 재현하기 어려움
* 정말 간단한 이미지 빌드 빌드에 사용

```
$ docker run -it ubuntu /bin/bash
apt-get -yqq update
apt-get -y install apache2
exit
```

* 최신 우분투 이미지 설치
* 소프트웨어 업데이트
* 아파치 설치
* 컨테이너에서 나오기

```
$ docker commit 6f57f37d dockerwithsteve/apache2

$ docker commit -m="new apache2 image" --author="dockerwithsteve" 6f57f37d2028 dockerwithsteve/apache2:webserver

$ docker inspect dockerwithsteve/apache2

```

* 해당 컨테이너 이름 지정(컨테이너 생성지 컨테이너 이름을 지정하지 않으면 도커에서 자동으로 이름을 부여함 이 이름으로 구분하기 어려우니 위의 명령어를 통해서 컨테이너 이름 apache2을 지정 )
* 도커 커밋(깃 커밋이랑 개념적으로 비슷하다고 생각하면된다.)
* 해당 컨테이너의 정보(OS 정보 기타등등...)

### Dockerfile
* 주로사용
* 이미지 빌드 과정을 도커파일에 저장하고 도커 빌드 명령어로 실행
* 실수가 적고 관리가 용이

#### dockerignore

* Context Root Directory에 위치
* 이미지 파일에 포함하고 싶지 않은 파일, 디렉토리 지정
	* 이미지를 크게 만드는 만드는 불필요파일 (node_module 깃이그노어랑 비슷한 느낌으로 이해하면됨)
	* 보안 때문에 넣으면 안되는 파일들

#### dockerignore 패턴

규칙         | 동작
:--------- | :--------------------------------------------------------------------
_/temp_    | 서브디렉터리에서 temp로 시작하는 파일과 디렉토리 제외 /somedir/tempoaray.txt /somedir/temp.
_/_/temp/* | 2레벨 이상의 디렉토리에 있는 temp로 시작하는 파일, 디렉토리 제외 /somedir/subdir/tempoaray.txt
_*/_.java  | 깊이에 상관 없는 모든 java 소스파일 제외
temp?      | temp로 시작하는 5글자 파일, 디렉토리제외 /tempa /tempb

#### Dockerfile
* 첫 줄부터 차례로 실행
* 한 라인이 실행 될 때 마다 이미지에 레이어 추가
* 실행 중에 에러가 나면 에러가 났던 곳부터 실행
* 첫 줄은 항상 `from`으로 시작
* `#`으로 시작하는 것은 주석으로 인식


```
$ mkdir docker_build
$ cd docker_build
$ vi Dockerfile
```
* `Dockerfile`에 아래 스크립트 작성

```
FROM ubuntu:16.04
# 우분투 16.10 버전에 이미지를 다운받아서 그것을 베이스 이미지로 하여 빌드를 진행한다
MAINTAINER cheese10 <cheese10yun@gmail.com>
# 이미지를 만든 사람의 이름과 이메일 작성

RUN apt-get update && apt-get install -y openssh-server
# 이미지를 빌드하면서 실행할 명령어들을 기술한다. 패키지 업데이트, `openssh-server` 설치

RUN mkdir /var/run/sshd
# `/var/run/sshd` 디렉토리 생성
RUN echo 'root:screencast' | chpasswd
# 루트 페스워드 설정

RUN sed -i 's/PermitRootLogin prohibit-password/PermitRootLogin yes/' /etc/ssh/sshd_config

# SSH login fix. Otherwise user is kicked off after login
RUN sed 's@session\s*required\s*pam_loginuid.so@session optional pam_loginuid.so@g' -i /etc/pam.d/sshd

ENV NOTVISIBLE "in users profile"
# 환경변수 설정
RUN echo "export VISIBLE=now" >> /etc/profile

EXPOSE 22
# 기본 포트(22) 노출
CMD ["/usr/sbin/sshd", "-D"]
```

#### Dockerfile Build
```
docker build -t "cheese10/sshd" .
```

* `cheese10` DockerHub 아이디
* 반드시 끝에 .  작성해야한다.

#### Docker histoy

```
$ docker history cheese10/sshd

MAGE               CREATED             CREATED BY                                      SIZE                COMMENT
2e71cdd83343        3 minutes ago       /bin/sh -c #(nop)  CMD ["/usr/sbin/sshd" "...   0 B
177542344914        3 minutes ago       /bin/sh -c #(nop)  EXPOSE 22/tcp                0 B
011444b0b719        3 minutes ago       /bin/sh -c echo "export VISIBLE=now" >> /e...   594 B
6fe8c68cde88        3 minutes ago       /bin/sh -c #(nop)  ENV NOTVISIBLE=in users...   0 B
9e35d3142ca8        3 minutes ago       /bin/sh -c sed 's@session\s*required\s*pam...   2.13 kB
ed9d38c7cfbe        3 minutes ago       /bin/sh -c sed -i 's/PermitRootLogin prohi...   2.53 kB
c0af44bff61d        3 minutes ago       /bin/sh -c echo 'root:screencast' | chpasswd    775 B
9743513c4635        4 minutes ago       /bin/sh -c mkdir /var/run/sshd                  0 B
68f67bae0daf        4 minutes ago       /bin/sh -c apt-get update && apt-get insta...   95 MB
2e4d50135517        8 minutes ago       /bin/sh -c #(nop)  MAINTAINER cheese10y <c...   0 B
0ef2e08ed3fa        12 days ago         /bin/sh -c #(nop)  CMD ["/bin/bash"]            0 B
<missing>           12 days ago         /bin/sh -c mkdir -p /run/systemd && echo '...   7 B
<missing>           12 days ago         /bin/sh -c sed -i 's/^#\s*\(deb.*universe\...   1.9 kB
<missing>           12 days ago         /bin/sh -c rm -rf /var/lib/apt/lists/*          0 B
<missing>           12 days ago         /bin/sh -c set -xe   && echo '#!/bin/sh' >...   745 B
<missing>           12 days ago         /bin/sh -c #(nop) ADD file:efb254bc677d66d...   130 MB
```
* 도커 실행 히스토리를 확인
* 이미지 이력관리가 가능하다.
* 가장 최근 작업이 상위에있다


#### ??

```
$ docker run -d -P --name test_sshd cheese10/sshd
bc188fa778ee35232946029911a1431ee5a755c0ff332e18feca58e6f09cae6e
```
* docker run 명령어로 컨테이너 생성
* `--name` 옵션으로 `test_sshd` 이름 부여

```
$ docker port test_sshd 22
0.0.0.0:32768
```

* 포트 노출

```
$ docker inspect --format {{.NetworkSettings.IPAddress}}'' test_sshd
```

* 아이피 확인
```
$ ssh root@localhost -p 32768
```

* `docker port` 명령어를 출력값인  `port 32768` 으로 ssh 접속
* 도커 이지지 빌드할때 작성한 페스워드 입력

### 빌드중 에러 발생

* 빌드 중 에러 발생
* 다시 빌드하면 에러 시점부터 빌드 시작
* 즉 케시해놓고 이미 완료된 작업은 다시 진행하지 않아 빠르게 빌드가 가능하다.

### 캐쉬를 무시하려면?

```
$ docker bulid --no-cache
```
* 케쉬때문에 필요 부분이 오래 걸릴수 있다.
* 위의 명령어를 입력하면 케시를 무시하고 처음부터 빌드 작업이 진행된다.

### 캐쉬를 일부만 원할 때 무시하려면 ?

```
FROM ubuntu:16.04
...
ENV UPDATE_AT 2017-01-01
RUN apt-get -qq update
...

```

* 환경 병수 설정으로 가능
* 약간 꼼수

### 22번 포트를 그대로 쓸수는 없을까?

```
$ docker run -d -p 22:22 --name test_sshd cheese10/sshd
$ ssh root@localhost
```

* `-p 22:22` 뒷부분 포트가 실제로 우리가 접속할 포트
* 기본 22가 기본포트 임으로 작성 22포트는 작성하지 않아도된다.

### 안 쓰는 컨테이너 지우기
```
$ docker stop test_sshd2 test_sshd3 test_sshd4

$ docker rm test_sshd2 test_sshd3 test_sshd4
```
* 한번에 여러개 도커 정지
* 정지된 도커 삭제


### Dockerfile 명령어 알오비기

#### ENV
* 환경 변수 설정
```
ENV <key><value>
ENV <key>=<value>

FROM ubuntu:16:04
ENV foo/bar
WORKDIR ${foo} # WORKDIR /bar
ADD .$foo # ADD ./bar
COPY \$foo /quux # COPY $foo /quux
```

#### CMD
```
CMD ["executable", "param1", "param2"]
CMD command param1 param2 # /bin/sh -c
CMD ["param1", "param2"] # ENTRYPOINT
```
* Dokcerfile에 하나의 CMD만 가능
* 여러개 CMD가 있을경우 마지막 것만 실행
* `docker build` 시점에 Dockerfile의 CMD 설정을 오버라이드 가능
* ENDPOINT 명령어의 파라미터로 사용될 수도 있음


#### CMD vs Run

* RUN 명령어를 실행할 때 마다 commit
* CMD는 bulid 시점에는 실행되지 않음에 주의
* CMD는 컨테이너를 run할 때 실행

#### ENTRYPOINT
* 컨테이너 생성할 때 실행

```
ENTRYPOINT ["executable", "param1", "param2"]
ENTRYPOINT command param1, param2
```
* Dockerfile 에 하나만 가능
* 여러 개 있다면ㅁ ㅏ지막 것맘ㄴ 실행
* `docker build` 시점에 Dockerfile의 ENDPOINT 설정을 오버라이드 가능
* CMD 명령어에서 파라미터를 받을 수 있음


```
CMD ["-T"]
ENDPOINT ["/usr/sbin/sshd"]
# 위의 두 명령어가 아래처럼 합쳐진다.

$ /usr/sbin/sshd -T
```

### WORKDIR
* RUN, CMD, ENTRYPOINT, ADD 등을 실행할 Directory를 지정
* Dockerfile에 여러번 사용가능
* 상대 경로도 사용 가능
* 해당 경로가 없으면 생성됨
* `docker run`에서 `-w` 옵션으로 오버라이드 가능

### USER
* RUN, CMD, ENTRYPOINT 등을 실행할 유저를 선택
* 지정하지 않으면 root로 실행
* 파라미터유 윶 ID나 UID를 지정

### VLOUME
* 호스트와 공유할 Directory 설정

```
VLOUME ["/data1", "/data2"]
VLOUME /data1, /data2

FROM ubuntu
RUN mkdir /myvol
RUN echo "hello world" > /myvol/greeting
VLOUME /myvol
```

### ADD
* 컨테이너에 파일 추가

```
ADD hom* /mydir/
# hom로 시작하는 모든 파일을 모두 mydir에 복사해라
ADD hom?.txt /mkdir
# hom?.txt ?에 어떤 문자가들어갈지 미정 그 모둔 파일을 mydir에 복사해라

ADD test relativDir/
# /가 없는 경우 상대 경로
ADD test /absoluteDir/
# /가 있는 경우 절대 경로

ADD http://example.com/foo.zip /mydir/bar.zip
# URL로 다운로드 받아서 해당 위치로 다운로드 시킨다.
ADD foo.tar.gz /mydir/
# 해당 파일을 특정 디렉토리로 지정
```

* 가져올 파일이 변경되면 ADD 명령으로 빌드 캐쉬가 무효화 될 수 있음에 주의
* URL이 인증을 필요로 한다면 사용불가
* 아래와 같은 방법으로 Context 외부에서 복사 불가

```
ADD ../test /mydir/
```

### COPY
* ADD와 거의 유사하지만 복사 기능만 제공
* 압축 해제 같은 복사 외의 기능은 제외
* Contest  외부에서 사용 불가

## Docker Compose

* 여러 개의 컨테이너를 할번에 올리고 내릴 수 있는 툴
* 로컬 개발 환경, 테스트 서버, CI 등의 환경에서 사용하면 편리
* 각 애플리케이션에 dockerfile을 정의하고, 서비스들을 `docker-compose.yml`에 설정

### 명령어

```
$ docker-compose up
$ docker-compose stop
$ docker-compose ps
```

## DockerHub에 이미지 올리기

```
$ docker login
Username: 'Your Username'
Password: 'Your Password'
Login Succeeded

$ docker push cheese10/sshd
$ docker pull cheese10/sshd
```
* Docker 로그인
* DockerHub push : `cheese10/sshd` 앞부분은 계정아이디, 도커 이름
* Dokcer pull : `cheese10/sshd` 앞부분은 계정아이디, 도커 이름


## Node.JS 실습

`DockerFile`
 ```
 FROM node:6.9-onbuild
 ```
 `app.js`
 ```
 var http = require("http");

 http.createServer(function(req, res){
	 res.writeHead(200, {"Content-Type" : "text/plain"});
	 res.write("Hello Wrold");
	 res.end();
	 }).listen(8080);
 ```

 `package.json`
 ```
 {
	 "name" : "my-nodejs",
	 "version" : "0.1.0",
	 "description" : "dokcer nodejs test",
	 "main": "app.js",
	 "scripts":{
		 "start" : "node app.js"
	 }
 }
 ```
### 이미지 빌드

```
$ docker build --tag my-nodejs .
$ docker run -i -t p- 8080:8080 my-nodejs
```

* `my-nodejs` 이름으로 이미지 빌드
* `8080` 번으로 외부 포트를 바라 보게 한다.


## 막정리

## 접속

docker ps -a

docker exec -it  c456623003b1 /bin/bash

c456623003b1 = container id
