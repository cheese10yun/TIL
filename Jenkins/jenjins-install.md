# AWS Jeinkins install


## AWS(Red Hat 계열)

## 설치전

* sudo su : sodu 권한으로 작업진행 할것
* yum update : 이후 작업 진행할것
* Inbound : 8080 열어 놓을것


### 설치
```
$ wget -O /etc/yum.repos.d/jenkins.repo http://pkg.jenkins-ci.org/redhat-stable/jenkins.repo

$ rpm --import http://pkg.jenkins-ci.org/redhat-stable/jenkins-ci.org.key
$ sudo yum install jenkins

$ service jenkins start
$ chkconfig jenkins on
```

### 확인

```
$ rpm -qa | grep jenkins
jenkins-2.64-1.1.noarch // jenkins version info

$ sudo service jenkins start
Starting Jenkins
```
