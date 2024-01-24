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

Создал [deploy](https://github.com/zatulik2606/Microservices/blob/main/Startsoftk8s/my-deployments.yaml)

Проверяю.

~~~
admin@ubuntu-hw:~/startk8s$ kubectl get deploy
NAME                  READY   UP-TO-DATE   AVAILABLE   AGE
netology-deployment   1/1     1            1           43s
multitool             1/1     1            1           43s

~~~


2. После запуска увеличить количество реплик работающего приложения до 2.

Увеличил.

~~~
admin@ubuntu-hw:~/startk8s$ kubectl get deploy
NAME                  READY   UP-TO-DATE   AVAILABLE   AGE
multitool             1/1     1            1           75s
netology-deployment   2/2     2            2           75s

~~~


3. Продемонстрировать количество подов до и после масштабирования.

До.

~~~
admin@ubuntu-hw:~/startk8s$ kubectl get po
NAME                                  READY   STATUS    RESTARTS   AGE
netology-deployment-b98d74c7f-shgcv   1/1     Running   0          12s
multitool-676d784fd9-j66fw            1/1     Running   0          12s


~~~

После

~~~
admin@ubuntu-hw:~/startk8s$ kubectl get po
NAME                                  READY   STATUS    RESTARTS   AGE
netology-deployment-b98d74c7f-shgcv   1/1     Running   0          93s
multitool-676d784fd9-j66fw            1/1     Running   0          93s
netology-deployment-b98d74c7f-rdjbl   1/1     Running   0          21s


~~~


4. Создать Service, который обеспечит доступ до реплик приложений из п.1.



Проверил.

~~~
admin@ubuntu-hw:~/startk8s$ kubectl get svc
NAME         TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)   AGE
kubernetes   ClusterIP   10.152.183.1     <none>        443/TCP   4h10m
myservice    ClusterIP   10.152.183.25    <none>        80/TCP    64m

~~~

5. Создать отдельный Pod с приложением multitool и убедиться с помощью `curl`, что из пода есть доступ до приложений из п.1.


~~~

admin@ubuntu-hw:~/startk8s$ kubectl exec multitool-676d784fd9-j66fw -- curl 10.152.183.212
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0<!DOCTYPE html>
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
100   612  100   612    0     0   709k      0 --:--:-- --:--:-- --:--:--  597k

~~~
------

### Задание 2. Создать Deployment и обеспечить старт основного контейнера при выполнении условий

1. Создать Deployment приложения nginx и обеспечить старт контейнера только после того, как будет запущен сервис этого приложения.

Создал [deploy-nginx](https://github.com/zatulik2606/Microservices/blob/main/Startsoftk8s/nginx.yaml)

2. Убедиться, что nginx не стартует. В качестве Init-контейнера взять busybox.

~~~
admin@ubuntu-hw:~/startk8s$ kubectl logs myapp-pod-5c98f46689-j574f 
Defaulted container "nginx" out of: nginx, init-myservice (init)
Error from server (BadRequest): container "nginx" in pod "myapp-pod-5c98f46689-j574f" is waiting to start: PodInitializing



admin@ubuntu-hw:~/startk8s$ kubectl get po
NAME                                  READY   STATUS     RESTARTS   AGE
netology-deployment-b98d74c7f-shgcv   1/1     Running    0          10m
multitool-676d784fd9-j66fw            1/1     Running    0          10m
netology-deployment-b98d74c7f-rdjbl   1/1     Running    0          9m17s
myapp-pod-5c98f46689-j574f            0/1     Init:0/1   0          90s

~~~


3. Создать и запустить Service. Убедиться, что Init запустился.


Создал [service](https://github.com/zatulik2606/Microservices/blob/main/Startsoftk8s/service.yaml)

Запустил и проверил.( pod поднялся)

~~~
admin@ubuntu-hw:~/startk8s$ kubectl get po
NAME                                  READY   STATUS    RESTARTS   AGE
netology-deployment-b98d74c7f-shgcv   1/1     Running   0          12m
multitool-676d784fd9-j66fw            1/1     Running   0          12m
netology-deployment-b98d74c7f-rdjbl   1/1     Running   0          11m
myapp-pod-5c98f46689-j574f            1/1     Running   0          3m28s

~~~

Проверяем логи.


~~~
admin@ubuntu-hw:~/startk8s$ kubectl logs myapp-pod-5c98f46689-j574f 
Defaulted container "nginx" out of: nginx, init-myservice (init)
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
