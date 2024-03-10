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
done (21s)
id: fhmekkh6sqmvqiemtpfg
folder_id: b1gleu995pjjtd5eficp
created_at: "2024-03-10T10:30:10Z"
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
  device_name: fhm33h70kcalvst45lqs
  auto_delete: true
  disk_id: fhm33h70kcalvst45lqs
network_interfaces:
  - index: "0"
    mac_address: d0:0d:ea:52:26:e6
    subnet_id: e9bdt908isbi5glca2oj
    primary_v4_address:
      address: 192.168.10.20
      one_to_one_nat:
        address: 158.160.112.81
        ip_version: IPV4
serial_port_settings:
  ssh_authorization: INSTANCE_METADATA
gpu_settings: {}
fqdn: masterk8s.ru-central1.internal
scheduling_policy: {}
network_settings:
  type: STANDARD
placement_policy: {}

done (34s)


......

~~~

VM создались в YC.

![vms](https://github.com/zatulik2606/Microservices/blob/main/installk8s/vms%20new.jpg)


Скачиваю kubespray из репозитория


~~~
yc-user@masterk8s:~$ sudo git clone https://github.com/kubernetes-sigs/kubespray
Cloning into 'kubespray'...
remote: Enumerating objects: 73361, done.
remote: Counting objects: 100% (859/859), done.
remote: Compressing objects: 100% (565/565), done.
remote: Total 73361 (delta 399), reused 607 (delta 240), pack-reused 72502
Receiving objects: 100% (73361/73361), 23.32 MiB | 24.84 MiB/s, done.
Resolving deltas: 100% (41234/41234), done.

~~~


Устанавливаю уязвимости.

~~~
yc-user@masterk8s:~/kubespray$ sudo pip3 install -r requirements.txt
Collecting ansible==6.6.0
  Downloading ansible-6.6.0-py3-none-any.whl (42.3 MB)
     |████████████████████████████████| 42.3 MB 49 kB/s 
Collecting cryptography==41.0.4
  Using cached cryptography-41.0.4-cp37-abi3-manylinux_2_17_x86_64.manylinux2014_x86_64.whl (4.4 MB)
Collecting jinja2==3.1.2
  Using cached Jinja2-3.1.2-py3-none-any.whl (133 kB)
Collecting jmespath==1.0.1
  Using cached jmespath-1.0.1-py3-none-any.whl (20 kB)
Collecting MarkupSafe==2.1.3
  Using cached MarkupSafe-2.1.3-cp38-cp38-manylinux_2_17_x86_64.manylinux2014_x86_64.whl (25 kB)
Collecting netaddr==0.9.0
  Using cached netaddr-0.9.0-py3-none-any.whl (2.2 MB)
Collecting pbr==5.11.1
  Using cached pbr-5.11.1-py2.py3-none-any.whl (112 kB)
Collecting ruamel.yaml==0.17.35
  Using cached ruamel.yaml-0.17.35-py3-none-any.whl (112 kB)
Collecting ruamel.yaml.clib==0.2.8
  Using cached ruamel.yaml.clib-0.2.8-cp38-cp38-manylinux_2_5_x86_64.manylinux1_x86_64.whl (596 kB)
Collecting ansible-core~=2.13.6
  Downloading ansible_core-2.13.13-py3-none-any.whl (2.1 MB)
     |████████████████████████████████| 2.1 MB 61.0 MB/s 
Collecting cffi>=1.12
  Downloading cffi-1.16.0-cp38-cp38-manylinux_2_17_x86_64.manylinux2014_x86_64.whl (444 kB)
     |████████████████████████████████| 444 kB 33 kB/s 
Requirement already satisfied: PyYAML>=5.1 in /usr/lib/python3/dist-packages (from ansible-core~=2.13.6->ansible==6.6.0->-r requirements.txt (line 1)) (5.3.1)
Collecting resolvelib<0.9.0,>=0.5.3
  Downloading resolvelib-0.8.1-py2.py3-none-any.whl (16 kB)
Collecting packaging
  Downloading packaging-24.0-py3-none-any.whl (53 kB)
     |████████████████████████████████| 53 kB 1.8 MB/s 
Collecting pycparser
  Downloading pycparser-2.21-py2.py3-none-any.whl (118 kB)
     |████████████████████████████████| 118 kB 65.4 MB/s 
Installing collected packages: pycparser, cffi, cryptography, resolvelib, packaging, MarkupSafe, jinja2, ansible-core, ansible, jmespath, netaddr, pbr, ruamel.yaml.clib, ruamel.yaml
  Attempting uninstall: cryptography
    Found existing installation: cryptography 2.8
    Not uninstalling cryptography at /usr/lib/python3/dist-packages, outside environment /usr
    Can't uninstall 'cryptography'. No files were found to uninstall.
  Attempting uninstall: MarkupSafe
    Found existing installation: MarkupSafe 1.1.0
    Not uninstalling markupsafe at /usr/lib/python3/dist-packages, outside environment /usr
    Can't uninstall 'MarkupSafe'. No files were found to uninstall.
  Attempting uninstall: jinja2
    Found existing installation: Jinja2 2.10.1
    Not uninstalling jinja2 at /usr/lib/python3/dist-packages, outside environment /usr
    Can't uninstall 'Jinja2'. No files were found to uninstall.
Successfully installed MarkupSafe-2.1.3 ansible-6.6.0 ansible-core-2.13.13 cffi-1.16.0 cryptography-41.0.4 jinja2-3.1.2 jmespath-1.0.1 netaddr-0.9.0 packaging-24.0 pbr-5.11.1 pycparser-2.21 resolvelib-0.8.1 ruamel.yaml-0.17.35 ruamel.yaml.clib-0.2.8

~~~

Сделал конфиги hosts

~~~

yc-user@masterk8s:~/kubespray$ declare -a IPS=(158.160.112.81 158.160.123.120 158.160.102.62 158.160.127.245 158.160.119.24)
yc-user@masterk8s:~/kubespray$ sudo CONFIG_FILE=inventory/mycluster/hosts.yaml python3 contrib/inventory_builder/inventory.py ${IPS[@]}
DEBUG: Adding group all
DEBUG: Adding group kube_control_plane
DEBUG: Adding group kube_node
DEBUG: Adding group etcd
DEBUG: Adding group k8s_cluster
DEBUG: Adding group calico_rr
DEBUG: adding host node1 to group all
DEBUG: adding host node2 to group all
DEBUG: adding host node3 to group all
DEBUG: adding host node4 to group all
DEBUG: adding host node5 to group all
DEBUG: adding host node1 to group etcd
DEBUG: adding host node2 to group etcd
DEBUG: adding host node3 to group etcd
DEBUG: adding host node1 to group kube_control_plane
DEBUG: adding host node2 to group kube_control_plane
DEBUG: adding host node1 to group kube_node
DEBUG: adding host node2 to group kube_node
DEBUG: adding host node3 to group kube_node
DEBUG: adding host node4 to group kube_node
DEBUG: adding host node5 to group kube_node

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
