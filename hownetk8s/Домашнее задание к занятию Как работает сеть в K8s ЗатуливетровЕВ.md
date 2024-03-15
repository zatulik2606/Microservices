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


Запустил и проверил , что создалось.

~~~
root@debian:~/Microservices/hownetk8s/create# kubectl get svc -n app
NAME       TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)   AGE
frontend   ClusterIP   10.152.183.63    <none>        80/TCP    3m22s
backend    ClusterIP   10.152.183.131   <none>        80/TCP    3m9s
cache      ClusterIP   10.152.183.143   <none>        80/TCP    2m57s
root@debian:~/Microservices/hownetk8s/create# kubectl get pods -n app -o wide
NAME                        READY   STATUS    RESTARTS   AGE     IP             NODE        NOMINATED NODE   READINESS GATES
backend-6478c64696-4nlbf    1/1     Running   0          5m45s   10.1.169.100   ubuntu-hw   <none>           <none>
cache-575bd6d866-lpvzj      1/1     Running   0          5m36s   10.1.169.124   ubuntu-hw   <none>           <none>
frontend-7c96b4cbfb-cmjpb   1/1     Running   0          5m58s   10.1.169.127   ubuntu-hw   <none>           <none>
root@debian:~/Microservices/hownetk8s/create# kubectl get -n app deployments
NAME       READY   UP-TO-DATE   AVAILABLE   AGE
backend    1/1     1            1           6m5s
cache      1/1     1            1           5m56s
frontend   1/1     1            1           6m19s

~~~

## Подготовил конфги для траффика
[deaault](https://github.com/zatulik2606/Microservices/blob/main/hownetk8s/networkpolicy/network-policy-default.yaml)
[back](https://github.com/zatulik2606/Microservices/blob/main/hownetk8s/networkpolicy/network-policy-backend.yaml)
[cache](https://github.com/zatulik2606/Microservices/blob/main/hownetk8s/networkpolicy/network-policy-cache.yaml)


