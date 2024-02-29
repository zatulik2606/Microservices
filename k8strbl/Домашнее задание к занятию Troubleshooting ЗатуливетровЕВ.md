# Домашнее задание к занятию Troubleshooting

### Цель задания

Устранить неисправности при деплое приложения.

### Чеклист готовности к домашнему заданию

1. Кластер K8s.

### Задание. При деплое приложение web-consumer не может подключиться к auth-db. Необходимо это исправить

1. Установить приложение по команде:
```shell
kubectl apply -f https://raw.githubusercontent.com/netology-code/kuber-homeworks/main/3.5/files/task.yaml
```

2. Выявить проблему и описать.

Указано ,что не находит namespace **web** и **data**.

~~~

kubectl apply -f https://raw.githubusercontent.com/netology-code/kuber-homeworks/main/3.5/files/task.yaml
Error from server (NotFound): error when creating "https://raw.githubusercontent.com/netology-code/kuber-homeworks/main/3.5/files/task.yaml": namespaces "web" not found
Error from server (NotFound): error when creating "https://raw.githubusercontent.com/netology-code/kuber-homeworks/main/3.5/files/task.yaml": namespaces "data" not found
Error from server (NotFound): error when creating "https://raw.githubusercontent.com/netology-code/kuber-homeworks/main/3.5/files/task.yaml": namespaces "data" not found

~~~





3. Исправить проблему, описать, что сделано.



Добавил и проверил.

~~~
admin@ubuntu-hw:~/k8strbl$ kubectl get namespace
NAME              STATUS   AGE
kube-system       Active   74d
kube-public       Active   74d
default           Active   74d
kube-node-lease   Active   36d
web               Active   3m59s
data              Active   3m53s

~~~

Файл с конфигами прошел.

~~~
admin@ubuntu-hw:~/k8strbl$ kubectl apply -f https://raw.githubusercontent.com/netology-code/kuber-homeworks/main/3.5/files/task.yaml
deployment.apps/web-consumer created
deployment.apps/auth-db created
service/auth-db created

~~~

Посмотрим корректность

~~~
admin@ubuntu-hw:~/k8strbl$ kubectl get pod -n web
NAME                            READY   STATUS    RESTARTS   AGE
web-consumer-5f87765478-8llh4   1/1     Running   0          3m27s
web-consumer-5f87765478-c7ccs   1/1     Running   0          3m28s


admin@ubuntu-hw:~/k8strbl$ kubectl get all -n data
NAME                           READY   STATUS    RESTARTS   AGE
pod/auth-db-7b5cdbdc77-xlt29   1/1     Running   0          4m18s

NAME              TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)   AGE
service/auth-db   ClusterIP   10.152.183.152   <none>        80/TCP    4m20s

NAME                      READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/auth-db   1/1     1            1           4m21s

NAME                                 DESIRED   CURRENT   READY   AGE
replicaset.apps/auth-db-7b5cdbdc77   1         1         1       4m20s



~~~

Контейнер внутри не находит auth-db.

~~~
admin@ubuntu-hw:~/k8strbl$ kubectl logs pod/web-consumer-5f87765478-c7ccs -n web
curl: (6) Couldn't resolve host 'auth-db'
curl: (6) Couldn't resolve host 'auth-db'
curl: (6) Couldn't resolve host 'auth-db'
curl: (6) Couldn't resolve host 'auth-db'
curl: (6) Couldn't resolve host 'auth-db'
curl: (6) Couldn't resolve host 'auth-db'

~~~

Захожу в контейнер.

~~~
admin@ubuntu-hw:~/k8strbl$ kubectl exec -it pod/web-consumer-5f87765478-c7ccs -n web -c busybox -- bin/sh
bin/sh: shopt: not found
[ root@web-consumer-5f87765478-c7ccs:/ ]$ 
[ root@web-consumer-5f87765478-c7ccs:/ ]$ curl 10.152.183.152
<!DOCTYPE html>
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

~~~

Видим что проблема заключается конкретно, что контейнер не знает имя auth-db




4. Продемонстрировать, что проблема решена.


### Правила приёма работы

1. Домашняя работа оформляется в своём Git-репозитории в файле README.md. Выполненное домашнее задание пришлите ссылкой на .md-файл в вашем репозитории.
2. Файл README.md должен содержать скриншоты вывода необходимых команд, а также скриншоты результатов.
3. Репозиторий должен содержать тексты манифестов или ссылки на них в файле README.md.
