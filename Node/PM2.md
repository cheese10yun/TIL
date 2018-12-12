## PM2

## 목차

- [PM2](#pm2)
- [목차](#%EB%AA%A9%EC%B0%A8)
- [PM2 소개](#pm2-%EC%86%8C%EA%B0%9C)
- [PM2 설치](#pm2-%EC%84%A4%EC%B9%98)
- [PM2 명령어](#pm2-%EB%AA%85%EB%A0%B9%EC%96%B4)
- [쉘 스크립트로 PM2 제어](#%EC%89%98-%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8%EB%A1%9C-pm2-%EC%A0%9C%EC%96%B4)
    - [클러스터 모드](#%ED%81%B4%EB%9F%AC%EC%8A%A4%ED%84%B0-%EB%AA%A8%EB%93%9C)

## PM2 소개

개발 중에 에러를 만나면 노드 서버가 강제로 죽어 버리는 경우를 빈번하게 맞이하게 됩니다. 이럴 때 앱을 재실행해주는 기능도 담당하고 있어 실제 노드 서버에서는 필수적인 패키지이라고 할 수 있습니다. 물론 이밖에 다양한 기능들을 재공해 주고 있습니다. 기능을 크게 정리하면 다음과 같습니다.


* **앱에서 충돌이 발생할 경우 앱을 자동으로 다시 시작**
* **런타임 성능 및 자원 소비에 대한 통찰력을 획득**
* **성능 향상을 위해 설정을 동적으로 수정**
* **클러스터링을 제어**

## PM2 설치

```
[sudo] npm install pm2 -g
```

## PM2 명령어

```
pm2 start  <실행시킬 서버. js> --name <AppName>
```

제 환경에서는 www 파일로 노드 서버를 시행시키고 있어 위 그림과 같은 명령어로 PM2를 실행합니다. App Name을 설정하는 것을 권장해 드립니다. 아래 소개하여드릴 명령어를 App Name으로 쉽게 제어할 수 있어집니다.

```
pm2 list
```

pm2 리스트를 보여주는 명령어입니다. 설정하신 App Name 값으로 실행되신 걸 확인하실 수 있습니다.

```
pm2 stop <app_name>
pm2 restart <app_name>
pm2 delete <app_name>
```

간단한 명령어이므로 설명은 생략하겠습니다. 명령어 뜻 그대로 해석하시면 됩니다. 위에서 설명했던 것처럼 App_name으로 편리하게 pm2 명령어를 제어할 수 있습니다.

```
pm2 show <app_name>
```
pm2 정보뿐만이 아니라 노드 버전, 로그 위치, 기타 등등 다양한 정보를 확인하실 수 있습니다. 또 git을 사용
하신다면 branch 정보 등 간략한 정보도 출력됩니다.

## 쉘 스크립트로 PM2 제어
사실 이 내용을 포스팅하기 위해서 시작했습니다. 이 부분만 따로 올리기에는 포스팅 내용이 너무 짧아서 조금씩 넣다 보니 이렇게 길어…. 졌습니다.

서버를 시작, 정지, 재시작하는 경우는 정말 빈번하게 발생합니다. 그래서 저희는 start.sh, restart.sh, stop.sh 쉘 스크립트를 통해서 start, restart, stop 작업을 진행시킵니다.항상 그렇듯 반복되는 귀찮은 작업은 간략화시키는 것이 좋습니다.

`start`

```#!/bin/bash

pm2 reload node_yun;
echo 'Reload pm2 demon...';
sleep 1;
sudo service nginx restart;
echo 'Restart nginx server...';
echo 'All Done!'
exit;
```



`stop.sh`

```
#!/bin/bash

pm2 stop node_yun;
echo 'Stop node_yun by pm2';
sleep 1;
sudo service nginx stop;
echo 'Stop nginx server...';
echo 'All Done!';
exit;
```

`restart.sh`

```
#!/bin/bash

pm2 delete node_yun;
pm2 start /home/ec2-user/node-yun/bin/www -i 0 --name node_yun;
echo 'start node_yun by pm2';
sleep 1;
sudo service nginx start;
echo 'Start nginx server...';
echo 'All Done!';
exit;
```

### 클러스터 모드

pm2를 실행시킬 때 -i 옵션을 사용하면 클러스터 모드로 실행이 됩니다. -i 뒤에 0을 지정하면 사용 가능한 CPU가 모두 실행이 됩니다. 간단하게 클러스터 모드를 실행시킬 수 있습니다.

[Yun Blog PM2](https://cheese10yun.github.io/PM2) 이곳에 좀더 자세한 설명이 있습니다.
