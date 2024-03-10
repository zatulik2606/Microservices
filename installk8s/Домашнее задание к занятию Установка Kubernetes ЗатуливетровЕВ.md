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




Создал скрипт по установке в yc.

~~~
root@debianz:~/vms_yc# vim create-vms.sh
root@debianz:~/vms_yc# bash create-vms.sh
done (34s)
id: fhmgobkqd1g40eio6rc5
folder_id: b1gleu995pjjtd5eficp
created_at: "2024-03-10T09:35:28Z"
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
  device_name: fhmp4s3c7oqq106uhab7
  auto_delete: true
  disk_id: fhmp4s3c7oqq106uhab7
network_interfaces:
  - index: "0"
    mac_address: d0:0d:10:c2:e9:a6
    subnet_id: e9bdt908isbi5glca2oj
    primary_v4_address:
      address: 192.168.10.30
      one_to_one_nat:
        address: 158.160.121.206
        ip_version: IPV4
serial_port_settings:
  ssh_authorization: INSTANCE_METADATA
gpu_settings: {}
fqdn: masterk8s.ru-central1.internal
scheduling_policy: {}
network_settings:
  type: STANDARD
placement_policy: {}

done (5m31s)
id: fhm09r72tst53569garp
folder_id: b1gleu995pjjtd5eficp
created_at: "2024-03-10T09:36:03Z"
name: worker1
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
  device_name: fhmf03eanvh5bk56rq3j
  auto_delete: true
  disk_id: fhmf03eanvh5bk56rq3j
network_interfaces:
  - index: "0"
    mac_address: d0:0d:4e:ce:2e:f3
    subnet_id: e9bdt908isbi5glca2oj
    primary_v4_address:
      address: 192.168.10.26
      one_to_one_nat:
        address: 158.160.123.120
        ip_version: IPV4
serial_port_settings:
  ssh_authorization: INSTANCE_METADATA
gpu_settings: {}
fqdn: worker1.ru-central1.internal
scheduling_policy: {}
network_settings:
  type: STANDARD
placement_policy: {}

done (35s)
id: fhmi8tar9ab9he32d0mk
folder_id: b1gleu995pjjtd5eficp
created_at: "2024-03-10T09:41:35Z"
name: worker2
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
  device_name: fhm0jokmqvpg0bcb97pp
  auto_delete: true
  disk_id: fhm0jokmqvpg0bcb97pp
network_interfaces:
  - index: "0"
    mac_address: d0:0d:12:47:55:b4
    subnet_id: e9bdt908isbi5glca2oj
    primary_v4_address:
      address: 192.168.10.31
      one_to_one_nat:
        address: 158.160.103.65
        ip_version: IPV4
serial_port_settings:
  ssh_authorization: INSTANCE_METADATA
gpu_settings: {}
fqdn: worker2.ru-central1.internal
scheduling_policy: {}
network_settings:
  type: STANDARD
placement_policy: {}

done (33s)
id: fhm4odjqgt5jaqp53f71
folder_id: b1gleu995pjjtd5eficp
created_at: "2024-03-10T09:42:11Z"
name: worker3
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
  device_name: fhmf024jphbu993tqhan
  auto_delete: true
  disk_id: fhmf024jphbu993tqhan
network_interfaces:
  - index: "0"
    mac_address: d0:0d:4c:36:7a:87
    subnet_id: e9bdt908isbi5glca2oj
    primary_v4_address:
      address: 192.168.10.19
      one_to_one_nat:
        address: 51.250.87.193
        ip_version: IPV4
serial_port_settings:
  ssh_authorization: INSTANCE_METADATA
gpu_settings: {}
fqdn: worker3.ru-central1.internal
scheduling_policy: {}
network_settings:
  type: STANDARD
placement_policy: {}

done (32s)
id: fhmruld7rp0c27fppast
folder_id: b1gleu995pjjtd5eficp
created_at: "2024-03-10T09:42:46Z"
name: worker4
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
  device_name: fhmdgacdt87llpu09phh
  auto_delete: true
  disk_id: fhmdgacdt87llpu09phh
network_interfaces:
  - index: "0"
    mac_address: d0:0d:1b:f5:5a:7d
    subnet_id: e9bdt908isbi5glca2oj
    primary_v4_address:
      address: 192.168.10.25
      one_to_one_nat:
        address: 158.160.99.198
        ip_version: IPV4
serial_port_settings:
  ssh_authorization: INSTANCE_METADATA
gpu_settings: {}
fqdn: worker4.ru-central1.internal
scheduling_policy: {}
network_settings:
  type: STANDARD
placement_policy: {}


~~~

VM создались в YC.

![vms](https://github.com/zatulik2606/Microservices/blob/main/installk8s/vms%20new1.jpg)


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
