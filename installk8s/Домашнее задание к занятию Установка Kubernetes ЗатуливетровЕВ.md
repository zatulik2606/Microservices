# Домашнее задание к занятию «Установка Kubernetes» 

### Цель задания

Установить кластер K8s.

### Чеклист готовности к домашнему заданию

1. Развёрнутые ВМ с ОС Ubuntu 20.04-lts.


### Инструменты и дополнительные материалы, которые пригодятся для выполнения задания

1. [Инструкция по установке kubeadm](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/create-cluster-kubeadm/).
2. [Документация kubespray](https://kubespray.io/).

-----

### Задание 1. Установить кластер k8s с 1 master node

1. Подготовка работы кластера из 5 нод: 1 мастер и 4 рабочие ноды.
2. В качестве CRI — containerd.
3. Запуск etcd производить на мастере.
4. Способ установки выбрать самостоятельно.



## Решение


Сделал предварительные действия по установке

~~~
root@debianz:~/vms_yc# yc vpc network create  --name net --labels my-label=netology --description "net yc"
id: enptlcvr948j76d45r8p
folder_id: b1gleu995pjjtd5eficp
created_at: "2024-03-10T08:14:28Z"
name: net
description: net yc
labels:
  my-label: netology
default_security_group_id: enpfaoahbef1mrldpp7e

root@debianz:~/vms_yc# yc vpc subnet create  --name my-subnet --zone ru-central1-a --range 192.168.10.0/24 --network-name net --description "subnet yc"
id: e9bdt908isbi5glca2oj
folder_id: b1gleu995pjjtd5eficp
created_at: "2024-03-10T08:15:53Z"
name: my-subnet
description: subnet yc
network_id: enptlcvr948j76d45r8p
zone_id: ru-central1-a
v4_cidr_blocks:
  - 192.168.10.0/24


~~~




Создал и запустил скрипт по установке в yc.

~~~
root@debianz:~/vms_yc# bash create-vms.sh
done (30s)
id: fhmhp7cpkc6i41ql38p1
folder_id: b1gleu995pjjtd5eficp
created_at: "2024-03-12T05:32:21Z"
name: masterk8s
zone_id: ru-central1-a
platform_id: standard-v2
resources:
  memory: "2147483648"
  cores: "2"
  core_fraction: "100"
status: RUNNING
metadata_options:
  gce_http_endpoint: ENABLED
  aws_v1_http_endpoint: ENABLED
  gce_http_token: ENABLED
  aws_v1_http_token: DISABLED
boot_disk:
  mode: READ_WRITE
  device_name: fhm75159om95485pstve
  auto_delete: true
  disk_id: fhm75159om95485pstve
network_interfaces:
  - index: "0"
    mac_address: d0:0d:11:c9:d9:9a
    subnet_id: e9bdt908isbi5glca2oj
    primary_v4_address:
      address: 192.168.10.3
      one_to_one_nat:
        address: 130.193.36.214
        ip_version: IPV4
serial_port_settings:
  ssh_authorization: INSTANCE_METADATA
gpu_settings: {}
fqdn: masterk8s.ru-central1.internal
scheduling_policy: {}
network_settings:
  type: STANDARD
placement_policy: {}

done (31s)

...
~~~

VM создались в YC.

![vms]()


Скачиваю kubespray из репозитория


~~~
yc-user@masterk8s:~$ sudo git clone https://github.com/kubernetes-sigs/kubespray
Cloning into 'kubespray'...
remote: Enumerating objects: 73380, done.
remote: Counting objects: 100% (10/10), done.
remote: Compressing objects: 100% (10/10), done.
remote: Total 73380 (delta 2), reused 0 (delta 0), pack-reused 73370
Receiving objects: 100% (73380/73380), 23.21 MiB | 22.17 MiB/s, done.
Resolving deltas: 100% (41348/41348), done.


~~~


Устанавливаю зависимости.

~~~


~~~

Скачиваем инвенторку.

~~~
yc-user@masterk8s:~/kubespray$ sudo cp -rfp inventory/sample inventory/myclaster


~~~


Сделал конфиги hosts

~~~



~~~


## Дополнительные задания (со звёздочкой)

**Настоятельно рекомендуем выполнять все задания под звёздочкой.** Их выполнение поможет глубже разобраться в материале.   
Задания под звёздочкой необязательные к выполнению и не повлияют на получение зачёта по этому домашнему заданию. 

------
### Задание 2*. Установить HA кластер

1. Установить кластер в режиме HA.
2. Использовать нечётное количество Master-node.
3. Для cluster ip использовать keepalived или другой способ.

### Правила приёма работы

1. Домашняя работа оформляется в своем Git-репозитории в файле README.md. Выполненное домашнее задание пришлите ссылкой на .md-файл в вашем репозитории.
2. Файл README.md должен содержать скриншоты вывода необходимых команд `kubectl get nodes`, а также скриншоты результатов.
3. Репозиторий должен содержать тексты манифестов или ссылки на них в файле README.md.
