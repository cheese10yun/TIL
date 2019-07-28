# 1주 차 : 웹 서버 구성

서비스 운영에 기본이 되는 웹 서버를 로컬 환경, 클라우드 환경, 컨테이너 환경에 각각 구축하며 각 환경의 장, 단점과 전체적인 서버 아키텍처에 대한 기본 지식을 습득합니다.

* 리눅스 기본 명령어 및 사용 방법
* 운영 모니터링 시 지표 확인에 도움될 정보
* VIM 사용 방법 : vim 기본 설명(검색/치환/구간 복사,잘라내기 등의 편집 기능)
* Virtual Box, Vagrant를 활용하여 VM 운영하기
* 로컬 환경에서 Nginx 설치
* Docker 설치, Docker hub를 통해 컨테이너 기반의 Nginx 설치
* AWS EC2 인스턴스 생성 후 해당 환경에서 Nginx 설치하여 외부에서 접속


# 2주차

worker thrad = auto = CPU 갯수

worker connection = 1024 , CPU 갯수 * 1024가 Best practices



systemctl enable nginx

* LB 그림 그리기
* CLB만 public network 하면 된다. 나머지는 VPN


# 3주차

- nginx proxy 설정
- nginx location → api endpint reverse proxy 설정
  - location 설정은 상위에 있을수록 우선순위가 높기 때문에 설정을 잘 해야한다.
  - proxy 웹서버 여역으로 요청을 그대로 전달
  - 해더값에 많은 정보가 있으니까 그대로 전달해ㅑ함
  - ios / android 에따라서 로드 벨런싱 할 수도 있음 (좋아 보이진 않음?...)
- front proxy reverse proxy 차이는 ?

# 4주차

## 네트워크

## 리눅스

# 5주차

##  Docker
###  Health Check
* EXITED, Health Check를 꼭 진행 해야한다. 

### docker 명령어
* `docker rm web`
  * rm / rmi: 컨테이너 / 이미지 삭제시 사용
  * `-f`을 이용해서 삭제

### Container Registry


