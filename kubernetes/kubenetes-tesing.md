# 시작 하기


```
$ minikube status
$ minikube stop
$ minikube start

```

##  워드프레스 배포


```
❯ kubectl apply -f wordpress-k8s.yml
deployment.apps/wordpress-mysql created
service/wordpress-mysql created
deployment.apps/wordpress created
service/wordpress created
```