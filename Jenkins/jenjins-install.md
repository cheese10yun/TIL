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

### Jenkins script Root 허용

```
Running shell scripts that have contain sudo commands in them from jenkins might not run as expected. To fix this, follow along
Simple steps:
1. On ubuntu based systems, run " $ sudo visudo "
2. this will open /etc/sudoers file.
3. If your jenkins user is already in that file, then modify to look like this:

jenkins ALL=(ALL) NOPASSWD: ALL
4. save the file by doing Ctrl+O  (dont save in tmp file. save in /etc/sudoers, confirm overwrite)
5. Exit by doing Ctrl+X
6. Relaunch your jenkins job 
7. you shouldnt see that error message again :)
```

[Link](https://gist.github.com/hayderimran7/9246dd195f785cf4783d)

### 실제 사용법
```
$ sudo visudo
jenkins ALL=(ALL) NOPASSWD: ALL # Insert and Save..
$ sudo service jenkins restart
```
