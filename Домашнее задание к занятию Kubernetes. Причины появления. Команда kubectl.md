# Домашнее задание к занятию «Kubernetes. Причины появления. Команда kubectl»

### Цель задания

Для экспериментов и валидации ваших решений вам нужно подготовить тестовую среду для работы с Kubernetes. Оптимальное решение — развернуть на рабочей машине или на отдельной виртуальной машине MicroK8S.

------


### Задание 1. Установка MicroK8S

1. Установить MicroK8S на локальную машину или на удалённую виртуальную машину.
2. Установить dashboard.

Установили добавил dashboard.

![k8s](https://github.com/zatulik2606/my_own_collection_new/blob/wallpapers/microk8s%20status.png)


3. Сгенерировать сертификат для подключения к внешнему ip-адресу.

Добавил IP 


sudo vim /var/snap/microk8s/current/certs/csr.conf.template


IP. 3 = 158.160.38.134


Обновил сертификат

sudo microk8s refresh-certs --cert front-proxy-client.crt


------

### Задание 2. Установка и настройка локального kubectl
1. Установить на локальную машину kubectl.
2. Настроить локально подключение к кластеру.
3. Подключиться к дашборду с помощью port-forward.


Проверяем get nodes на виртуалке.


![k8s](https://github.com/zatulik2606/my_own_collection_new/blob/wallpapers/kubectl%20get%20nodes%20vm.png)


Проверяем get nodes на локалке

![k8s](https://github.com/zatulik2606/my_own_collection_new/blob/wallpapers/kubectl%20get%20nodes%20local.png)

Форвардим порт 


kubectl port-forward -n kube-system service/kubernetes-dashboard 10443:443 --address 0.0.0.0


Смотрим ,что получилось в браузере

![k8s](https://github.com/zatulik2606/my_own_collection_new/blob/wallpapers/dashboard.jpg)





------
