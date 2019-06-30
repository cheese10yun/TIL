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