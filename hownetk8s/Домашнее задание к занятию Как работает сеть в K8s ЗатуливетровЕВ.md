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


~~~

## Подготовил конфги для траффика
[default](https://github.com/zatulik2606/Microservices/blob/main/hownetk8s/networkpolicy/network-policy-default.yaml)

[back](https://github.com/zatulik2606/Microservices/blob/main/hownetk8s/networkpolicy/network-policy-backend.yaml)

[cache](https://github.com/zatulik2606/Microservices/blob/main/hownetk8s/networkpolicy/network-policy-cache.yaml)

Проверяю.

~~~

  Policy Types: Ingress



~~~


