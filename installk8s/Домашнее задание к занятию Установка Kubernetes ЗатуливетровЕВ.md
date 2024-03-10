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

<details><summary>Подробнее</summary>
```
root@debianz:~/vms_yc# bash create-vms.sh
done (29s)
id: fhmuh55k74omq5mdbts2
folder_id: b1gleu995pjjtd5eficp
created_at: "2024-03-10T08:23:27Z"
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
  device_name: fhm0c1ku3r91obakhksa
  auto_delete: true
  disk_id: fhm0c1ku3r91obakhksa
network_interfaces:
  - index: "0"
    mac_address: d0:0d:1e:89:4b:43
    subnet_id: e9bdt908isbi5glca2oj
    primary_v4_address:
      address: 192.168.10.6
      one_to_one_nat:
        address: 158.160.105.17
        ip_version: IPV4
serial_port_settings:
  ssh_authorization: INSTANCE_METADATA
gpu_settings: {}
fqdn: masterk8s.ru-central1.internal
scheduling_policy: {}
network_settings:
  type: STANDARD
placement_policy: {}

done (33s)
id: fhm583vblkeaicl22uem
folder_id: b1gleu995pjjtd5eficp
created_at: "2024-03-10T08:23:57Z"
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
  device_name: fhmd809lkvh17v84r67p
  auto_delete: true
  disk_id: fhmd809lkvh17v84r67p
network_interfaces:
  - index: "0"
    mac_address: d0:0d:54:0f:eb:ad
    subnet_id: e9bdt908isbi5glca2oj
    primary_v4_address:
      address: 192.168.10.33
      one_to_one_nat:
        address: 158.160.99.213
        ip_version: IPV4
serial_port_settings:
  ssh_authorization: INSTANCE_METADATA
gpu_settings: {}
fqdn: worker1.ru-central1.internal
scheduling_policy: {}
network_settings:
  type: STANDARD
placement_policy: {}

done (30s)
id: fhmp70ovf78kqgso1hu9
folder_id: b1gleu995pjjtd5eficp
created_at: "2024-03-10T08:24:31Z"
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
  device_name: fhmv9lg9jj263vf0p9k6
  auto_delete: true
  disk_id: fhmv9lg9jj263vf0p9k6
network_interfaces:
  - index: "0"
    mac_address: d0:0d:19:38:31:f7
    subnet_id: e9bdt908isbi5glca2oj
    primary_v4_address:
      address: 192.168.10.30
      one_to_one_nat:
        address: 158.160.109.218
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
id: fhma9or9qgpam8p3fgnj
folder_id: b1gleu995pjjtd5eficp
created_at: "2024-03-10T08:25:03Z"
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
  device_name: fhmlkskiultos8tt3j3s
  auto_delete: true
  disk_id: fhmlkskiultos8tt3j3s
network_interfaces:
  - index: "0"
    mac_address: d0:0d:a4:e3:69:d4
    subnet_id: e9bdt908isbi5glca2oj
    primary_v4_address:
      address: 192.168.10.5
      one_to_one_nat:
        address: 158.160.99.110
        ip_version: IPV4
serial_port_settings:
  ssh_authorization: INSTANCE_METADATA
gpu_settings: {}
fqdn: worker3.ru-central1.internal
scheduling_policy: {}
network_settings:
  type: STANDARD
placement_policy: {}

done (23s)
id: fhmb0ovuovrhavrpjikd
folder_id: b1gleu995pjjtd5eficp
created_at: "2024-03-10T08:25:38Z"
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
  device_name: fhm3dbbp7koj4nuqqtv1
  auto_delete: true
  disk_id: fhm3dbbp7koj4nuqqtv1
network_interfaces:
  - index: "0"
    mac_address: d0:0d:b0:63:fe:c7
    subnet_id: e9bdt908isbi5glca2oj
    primary_v4_address:
      address: 192.168.10.7
      one_to_one_nat:
        address: 158.160.116.209
        ip_version: IPV4
serial_port_settings:
  ssh_authorization: INSTANCE_METADATA
gpu_settings: {}
fqdn: worker4.ru-central1.internal
scheduling_policy: {}
network_settings:
  type: STANDARD
placement_policy: {}

```
</details>


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
