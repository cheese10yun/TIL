## AWS CLI를 통한 AWS config설정

* AWS S3 secret 정보들은 root/.aws/credentials 파일에 설정하는 것이 바람직하다.
* Region 설정 또한 이곳에 놓는 것이 바림직하다.


### AWS CLI 설치

```
brew install awscli
```

### AWS CLI 설정
```
$ aws configure
AWS Access Key ID [None]: ''
AWS Secret Access Key None]: ''
Default region name [None]: 'ap-northeast-2'
Default output format []: 'JSON'
```

* Access key, Secret Key 설정
* Region 설정
* output format 설정
* 기존에 입력값이 없을 경우 `None`이 표시 있을 경우 해당 값 표시

### S3 List Check

```
$ aws s3 ls

2016-10-26 12:09:19 clipplr.com
2016-06-21 13:14:47 elasticbeanstalk-ap-northeast-2-367033320748
2016-12-05 14:30:49 hc-backup-video
...
...
...
2016-12-01 12:47:48 hcpayment
2016-10-25 22:00:25 holdemclub
```

* AWS S3가 연결잘됬나 확인


### config 파일 위치

```
$ cd ~/.aws
$ ls
config      credentials
```

* `credentials` Access key, secret key 정보
* `config` S3 Region, ETC 정보
