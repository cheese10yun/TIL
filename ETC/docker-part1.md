##

## Docker 명령어

* docker start
* docker stop
* docker restar
* docker pause
* docker unpause
* docker wait
* docker kill
* docker attach
* docker exec

## docker compose

```
sudo pip install docker-compose
```
* 에로 생겼을 경우

* docker-compose up -d

* docker-compose exec database bash


## 도커 용량 이상
```
~ / Library / Containers / com.docker.docker
```
* 도커 파일 너무 크게 먹는 이슈
* 그 동안 설치했던 이미지들이 완전히 삭제 되지 않는 거같음
* 위의 경로에 데이터들이 남아있음

```
docker rm $(docker ps -a -q)
docker rmi $(docker images -q)
docker volume rm $(docker volume ls |awk '{print $2}')
rm -rf ~/Library/Containers/com.docker.docker/Data/*
```
* [link](https://github.com/docker/for-mac/issues/371)


## 참고링크
* [docker-compose 명령어](https://docs.docker.com/compose/reference/build/)
