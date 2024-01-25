# Домашнее задание к занятию «Запуск приложений в K8S»

### Цель задания

В тестовой среде для работы с Kubernetes, установленной в предыдущем ДЗ, необходимо развернуть Deployment с приложением, состоящим из нескольких контейнеров, и масштабировать его.

------

### Чеклист готовности к домашнему заданию

1. Установленное k8s-решение (например, MicroK8S).
2. Установленный локальный kubectl.
3. Редактор YAML-файлов с подключённым git-репозиторием.

------

### Инструменты и дополнительные материалы, которые пригодятся для выполнения задания

1. [Описание](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/) Deployment и примеры манифестов.
2. [Описание](https://kubernetes.io/docs/concepts/workloads/pods/init-containers/) Init-контейнеров.
3. [Описание](https://github.com/wbitt/Network-MultiTool) Multitool.

------

### Задание 1. Создать Deployment и обеспечить доступ к репликам приложения из другого Pod

1. Создать Deployment приложения, состоящего из двух контейнеров — nginx и multitool. Решить возникшую ошибку.

Создал   [deploy](https://github.com/zatulik2606/Microservices/blob/main/startk8snewversion/nginx-multitool.yaml)


Проверяю.

~~~

admin@ubuntu-hw:~/Microservices/startk8snewversion$ kubectl get deploy
NAME                  READY   UP-TO-DATE   AVAILABLE   AGE
dpl-nginx-multitool   1/1     1            1           91s

admin@ubuntu-hw:~/Microservices/startk8snewversion$ kubectl get po
NAME                                   READY   STATUS    RESTARTS   AGE
dpl-nginx-multitool-645c8c8575-7r7vn   2/2     Running   0          103s



~~~


2. После запуска увеличить количество реплик работающего приложения до 2.

Увеличил.

~~~
admin@ubuntu-hw:~/Microservices/startk8snewversion$ kubectl get deploy
NAME                  READY   UP-TO-DATE   AVAILABLE   AGE
dpl-nginx-multitool   2/2     2            2           4m2s
admin@ubuntu-hw:~/Microservices/startk8snewversion$ kubectl get rs
NAME                             DESIRED   CURRENT   READY   AGE
dpl-nginx-multitool-645c8c8575   2         2         2       4m11s

~~~


3. Продемонстрировать количество подов до и после масштабирования.

До.

~~~
admin@ubuntu-hw:~/Microservices/startk8snewversion$ kubectl get po
NAME                                   READY   STATUS    RESTARTS   AGE
dpl-nginx-multitool-645c8c8575-7r7vn   2/2     Running   0          103s



~~~

После

~~~

admin@ubuntu-hw:~/Microservices/startk8snewversion$ kubectl get po
NAME                                   READY   STATUS    RESTARTS   AGE
dpl-nginx-multitool-645c8c8575-7r7vn   2/2     Running   0          4m37s
dpl-nginx-multitool-645c8c8575-zj2bl   2/2     Running   0          42s

~~~


4. Создать Service, который обеспечит доступ до реплик приложений из п.1.

Создал [svc-nginx-multitool.yaml](https://github.com/zatulik2606/Microservices/blob/main/startk8snewversion/svc-nginx-multitool.yaml)

Проверил.

~~~
admin@ubuntu-hw:~/Microservices/startk8snewversion$ kubectl get svc
NAME                      TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)                    AGE
kubernetes                ClusterIP   10.152.183.1    <none>        443/TCP                    23h
svc-nginx-multitool-dz3   ClusterIP   10.152.183.93   <none>        80/TCP,443/TCP,31080/TCP   64s


~~~

5. Создать отдельный Pod с приложением multitool и убедиться с помощью `curl`, что из пода есть доступ до приложений из п.1.

Создал [deploy-multitool.yaml](https://github.com/zatulik2606/Microservices/blob/main/startk8snewversion/deploy-multitool.yaml)


Проверяю.

~~~

admin@ubuntu-hw:~/Microservices/startk8snewversion$ kubectl exec test-multitool -- curl svc-nginx-multitool-dz3:80
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0     0    0     0    0     0      0      0 --:--:--  0:00:01 --:--:--     0<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
    body {
        width: 35em;
        margin: 0 auto;
        font-family: Tahoma, Verdana, Arial, sans-serif;
    }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
100   612  100   612    0     0    277      0  0:00:02  0:00:02 --:--:--   277



~~~
------

### Задание 2. Создать Deployment и обеспечить старт основного контейнера при выполнении условий

1. Создать Deployment приложения nginx и обеспечить старт контейнера только после того, как будет запущен сервис этого приложения.

Создал [deploy-nginx-init.yaml](https://github.com/zatulik2606/Microservices/blob/main/startk8snewversion/deploy-nginx-init.yaml)

2. Убедиться, что nginx не стартует. В качестве Init-контейнера взять busybox.


~~~
admin@ubuntu-hw:~/Microservices/startk8snewversion$ kubectl logs dpl-nginx-init-78794459dc-98cz5
Defaulted container "nginx" out of: nginx, init-busybox (init)
Error from server (BadRequest): container "nginx" in pod "dpl-nginx-init-78794459dc-98cz5" is waiting to start: PodInitializing





~~~


3. Создать и запустить Service. Убедиться, что Init запустился.


Создал [service](https://github.com/zatulik2606/Microservices/blob/main/startk8snewversion/service.yaml)

Запустил и проверил.( pod поднялся)

~~~
admin@ubuntu-hw:~/Microservices/startk8snewversion$ kubectl get po
NAME                                   READY   STATUS    RESTARTS   AGE
dpl-nginx-multitool-645c8c8575-7r7vn   2/2     Running   0          34m
dpl-nginx-multitool-645c8c8575-zj2bl   2/2     Running   0          31m
test-multitool                         1/1     Running   0          23m
dpl-nginx-init-78794459dc-98cz5        1/1     Running   0          5m5s



~~~

Проверяем логи.


~~~

admin@ubuntu-hw:~/Microservices/startk8snewversion$ kubectl logs dpl-nginx-init-78794459dc-98cz5
Defaulted container "nginx" out of: nginx, init-busybox (init)
/docker-entrypoint.sh: /docker-entrypoint.d/ is not empty, will attempt to perform configuration
/docker-entrypoint.sh: Looking for shell scripts in /docker-entrypoint.d/
/docker-entrypoint.sh: Launching /docker-entrypoint.d/10-listen-on-ipv6-by-default.sh
10-listen-on-ipv6-by-default.sh: Getting the checksum of /etc/nginx/conf.d/default.conf
10-listen-on-ipv6-by-default.sh: Enabled listen on IPv6 in /etc/nginx/conf.d/default.conf
/docker-entrypoint.sh: Launching /docker-entrypoint.d/20-envsubst-on-templates.sh
/docker-entrypoint.sh: Configuration complete; ready for start up

~~~


4. Продемонстрировать состояние пода до и после запуска сервиса.

Было сделано выше.

------

### Правила приема работы

1. Домашняя работа оформляется в своем Git-репозитории в файле README.md. Выполненное домашнее задание пришлите ссылкой на .md-файл в вашем репозитории.
2. Файл README.md должен содержать скриншоты вывода необходимых команд `kubectl` и скриншоты результатов.
3. Репозиторий должен содержать файлы манифестов и ссылки на них в файле README.md.

------
