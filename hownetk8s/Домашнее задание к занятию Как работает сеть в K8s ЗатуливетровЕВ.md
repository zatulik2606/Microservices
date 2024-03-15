 Домашнее задание к занятию «Как работает сеть в K8s»

### Цель задания

Настроить сетевую политику доступа к подам.

### Чеклист готовности к домашнему заданию

1. Кластер K8s с установленным сетевым плагином Calico.

### Инструменты и дополнительные материалы, которые пригодятся для выполнения задания

1. [Документация Calico](https://www.tigera.io/project-calico/).
2. [Network Policy](https://kubernetes.io/docs/concepts/services-networking/network-policies/).
3. [About Network Policy](https://docs.projectcalico.org/about/about-network-policy).

-----

### Задание 1. Создать сетевую политику или несколько политик для обеспечения доступа

1. Создать deployment'ы приложений frontend, backend и cache и соответсвующие сервисы.
2. В качестве образа использовать network-multitool.
3. Разместить поды в namespace App.
4. Создать политики, чтобы обеспечить доступ frontend -> backend -> cache. Другие виды подключений должны быть запрещены.
5. Продемонстрировать, что трафик разрешён и запрещён.


## Решение 

## Подготовил конфиги

[front](https://github.com/zatulik2606/Microservices/blob/main/hownetk8s/create/frontend.yaml)

[back](https://github.com/zatulik2606/Microservices/blob/main/hownetk8s/create/backend.yaml)

[cache](https://github.com/zatulik2606/Microservices/blob/main/hownetk8s/create/cache.yaml)

[svc-front](https://github.com/zatulik2606/Microservices/blob/main/hownetk8s/create/svc-frontend.yaml)

[svc-back](https://github.com/zatulik2606/Microservices/blob/main/hownetk8s/create/svc-backend.yaml)

[svc-cache](https://github.com/zatulik2606/Microservices/blob/main/hownetk8s/create/svc-cache.yaml)


Запустил первичную настройку

~~~
root@debian:~/Microservices/hownetk8s/create# kubectl create namespace app
namespace/app created
root@debian:~/Microservices/hownetk8s/create# ls -ls
итого 24
4 -rw-r--r-- 1 root root 433 мар 15 11:25 backend.yaml
4 -rw-r--r-- 1 root root 425 мар 15 11:25 cache.yaml
4 -rw-r--r-- 1 root root 437 мар 15 11:25 frontend.yaml
4 -rw-r--r-- 1 root root 147 мар 15 11:25 svc-backend.yaml
4 -rw-r--r-- 1 root root 143 мар 15 11:25 svc-cache.yaml
4 -rw-r--r-- 1 root root 149 мар 15 11:25 svc-frontend.yaml
root@debian:~/Microservices/hownetk8s/create# kubectl apply -f frontend.yaml
deployment.apps/frontend created
root@debian:~/Microservices/hownetk8s/create# kubectl apply -f backend.yaml
deployment.apps/backend created
root@debian:~/Microservices/hownetk8s/create# kubectl apply -f cache.yaml
deployment.apps/cache created
root@debian:~/Microservices/hownetk8s/create# kubectl get -n app deployments
NAME       READY   UP-TO-DATE   AVAILABLE   AGE
frontend   1/1     1            1           34s
backend    1/1     1            1           25s
cache      1/1     1            1           14s


~~~


Настраиваю далее

~~~
root@debian:~/Microservices/hownetk8s/create# kubectl apply -f frontend.yaml
deployment.apps/frontend created
root@debian:~/Microservices/hownetk8s/create# kubectl apply -f backend.yaml
deployment.apps/backend created
root@debian:~/Microservices/hownetk8s/create# kubectl apply -f cache.yaml
deployment.apps/cache created
root@debian:~/Microservices/hownetk8s/create# kubectl get -n app deployments
NAME       READY   UP-TO-DATE   AVAILABLE   AGE
frontend   1/1     1            1           34s
backend    1/1     1            1           25s
cache      1/1     1            1           14s
root@debian:~/Microservices/hownetk8s/create# kubectl apply -f svc-frontend.yaml
service/frontend created
root@debian:~/Microservices/hownetk8s/create# kubectl apply -f svc-backend.yaml
service/backend created
root@debian:~/Microservices/hownetk8s/create# kubectl apply -f svc-cache.yaml
service/cache created
root@debian:~/Microservices/hownetk8s/create# kubectl get svc -n app
NAME       TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)   AGE
frontend   ClusterIP   10.152.183.238   <none>        80/TCP    32s
backend    ClusterIP   10.152.183.147   <none>        80/TCP    19s
cache      ClusterIP   10.152.183.151   <none>        80/TCP    12s
root@debian:~/Microservices/hownetk8s/create# kubectl get pods -n app -o wide
NAME                        READY   STATUS    RESTARTS   AGE     IP            NODE        NOMINATED NODE   READINESS GATES
frontend-7c96b4cbfb-rgmcx   1/1     Running   0          3m5s    10.1.169.67   ubuntu-hw   <none>           <none>
backend-6478c64696-l54dh    1/1     Running   0          2m56s   10.1.169.66   ubuntu-hw   <none>           <none>
cache-575bd6d866-tqcf8      1/1     Running   0          2m45s   10.1.169.68   ubuntu-hw   <none>           <none>

~~~

## Подготовил конфги для траффика
[default](https://github.com/zatulik2606/Microservices/blob/main/hownetk8s/networkpolicy/network-policy-default.yaml)

[back](https://github.com/zatulik2606/Microservices/blob/main/hownetk8s/networkpolicy/network-policy-backend.yaml)

[cache](https://github.com/zatulik2606/Microservices/blob/main/hownetk8s/networkpolicy/network-policy-cache.yaml)

Проверяю.

~~~

  Policy Types: Ingress



~~~


