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
oot@debianz:~/vms_yc# bash create-vms.sh
done (44s)
id: fhmhvg0khptjene4k2bd
folder_id: b1gleu995pjjtd5eficp
created_at: "2024-03-12T09:22:04Z"
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
  device_name: fhm4vr9f0opp8un78de2
  auto_delete: true
  disk_id: fhm4vr9f0opp8un78de2
network_interfaces:
  - index: "0"
    mac_address: d0:0d:11:fc:01:48
    subnet_id: e9bdt908isbi5glca2oj
    primary_v4_address:
      address: 192.168.10.29
      one_to_one_nat:
        address: 158.160.100.171
        ip_version: IPV4
serial_port_settings:
  ssh_authorization: INSTANCE_METADATA
gpu_settings: {}
fqdn: masterk8s.ru-central1.internal
scheduling_policy: {}
network_settings:
  type: STANDARD
placement_policy: {}

done (35s)

...
~~~

VM создались в YC.

![vms](https://github.com/zatulik2606/Microservices/blob/main/installk8s/vms%20new4.jpg)


Скачиваю kubespray из репозитория


~~~
yc-user@masterk8s:~$ sudo git clone https://github.com/kubernetes-sigs/kubespray
Cloning into 'kubespray'...
remote: Enumerating objects: 73402, done.
remote: Counting objects: 100% (30/30), done.
remote: Compressing objects: 100% (26/26), done.
remote: Total 73402 (delta 8), reused 16 (delta 3), pack-reused 73372
Receiving objects: 100% (73402/73402), 23.26 MiB | 20.01 MiB/s, done.
Resolving deltas: 100% (41356/41356), done.



~~~


Устанавливаю зависимости.

~~~
yc-user@masterk8s:~/kubespray$ sudo pip3.9 install -r requirements.txt
Collecting ansible==8.5.0 (from -r requirements.txt (line 1))
  Downloading ansible-8.5.0-py3-none-any.whl.metadata (7.9 kB)
Collecting cryptography==41.0.4 (from -r requirements.txt (line 2))
  Downloading cryptography-41.0.4-cp37-abi3-manylinux_2_28_x86_64.whl.metadata (5.2 kB)
Collecting jinja2==3.1.2 (from -r requirements.txt (line 3))
  Downloading Jinja2-3.1.2-py3-none-any.whl.metadata (3.5 kB)
Collecting jmespath==1.0.1 (from -r requirements.txt (line 4))
  Downloading jmespath-1.0.1-py3-none-any.whl.metadata (7.6 kB)
Collecting MarkupSafe==2.1.3 (from -r requirements.txt (line 5))
  Downloading MarkupSafe-2.1.3-cp39-cp39-manylinux_2_17_x86_64.manylinux2014_x86_64.whl.metadata (3.0 kB)
Collecting netaddr==0.9.0 (from -r requirements.txt (line 6))
  Downloading netaddr-0.9.0-py3-none-any.whl.metadata (5.1 kB)
Collecting pbr==5.11.1 (from -r requirements.txt (line 7))
  Downloading pbr-5.11.1-py2.py3-none-any.whl.metadata (1.3 kB)
Collecting ruamel.yaml==0.17.35 (from -r requirements.txt (line 8))
  Downloading ruamel.yaml-0.17.35-py3-none-any.whl.metadata (18 kB)
Collecting ruamel.yaml.clib==0.2.8 (from -r requirements.txt (line 9))
  Downloading ruamel.yaml.clib-0.2.8-cp39-cp39-manylinux_2_5_x86_64.manylinux1_x86_64.whl.metadata (2.2 kB)
Collecting ansible-core~=2.15.5 (from ansible==8.5.0->-r requirements.txt (line 1))
  Downloading ansible_core-2.15.9-py3-none-any.whl.metadata (7.0 kB)
Collecting cffi>=1.12 (from cryptography==41.0.4->-r requirements.txt (line 2))
  Downloading cffi-1.16.0-cp39-cp39-manylinux_2_17_x86_64.manylinux2014_x86_64.whl.metadata (1.5 kB)
Requirement already satisfied: PyYAML>=5.1 in /usr/lib/python3/dist-packages (from ansible-core~=2.15.5->ansible==8.5.0->-r requirements.txt (line 1)) (5.3.1)
Collecting packaging (from ansible-core~=2.15.5->ansible==8.5.0->-r requirements.txt (line 1))
  Downloading packaging-24.0-py3-none-any.whl.metadata (3.2 kB)
Collecting resolvelib<1.1.0,>=0.5.3 (from ansible-core~=2.15.5->ansible==8.5.0->-r requirements.txt (line 1))
  Downloading resolvelib-1.0.1-py2.py3-none-any.whl.metadata (4.0 kB)
Collecting importlib-resources<5.1,>=5.0 (from ansible-core~=2.15.5->ansible==8.5.0->-r requirements.txt (line 1))
  Downloading importlib_resources-5.0.7-py3-none-any.whl.metadata (2.8 kB)
Collecting pycparser (from cffi>=1.12->cryptography==41.0.4->-r requirements.txt (line 2))
  Downloading pycparser-2.21-py2.py3-none-any.whl.metadata (1.1 kB)
Downloading ansible-8.5.0-py3-none-any.whl (47.5 MB)
   ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 47.5/47.5 MB 4.6 MB/s eta 0:00:00
Downloading cryptography-41.0.4-cp37-abi3-manylinux_2_28_x86_64.whl (4.4 MB)
   ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 4.4/4.4 MB 17.1 MB/s eta 0:00:00
Downloading Jinja2-3.1.2-py3-none-any.whl (133 kB)
   ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 133.1/133.1 kB 6.5 MB/s eta 0:00:00
Downloading jmespath-1.0.1-py3-none-any.whl (20 kB)
Downloading MarkupSafe-2.1.3-cp39-cp39-manylinux_2_17_x86_64.manylinux2014_x86_64.whl (25 kB)
Downloading netaddr-0.9.0-py3-none-any.whl (2.2 MB)
   ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 2.2/2.2 MB 41.1 MB/s eta 0:00:00
Downloading pbr-5.11.1-py2.py3-none-any.whl (112 kB)
   ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 112.7/112.7 kB 2.4 MB/s eta 0:00:00
Downloading ruamel.yaml-0.17.35-py3-none-any.whl (112 kB)
   ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 112.9/112.9 kB 10.7 MB/s eta 0:00:00
Downloading ruamel.yaml.clib-0.2.8-cp39-cp39-manylinux_2_5_x86_64.manylinux1_x86_64.whl (562 kB)
   ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 562.1/562.1 kB 22.1 MB/s eta 0:00:00
Downloading ansible_core-2.15.9-py3-none-any.whl (2.2 MB)
   ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 2.2/2.2 MB 58.3 MB/s eta 0:00:00
Downloading cffi-1.16.0-cp39-cp39-manylinux_2_17_x86_64.manylinux2014_x86_64.whl (443 kB)
   ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 443.4/443.4 kB 24.4 MB/s eta 0:00:00
Downloading importlib_resources-5.0.7-py3-none-any.whl (24 kB)
Downloading resolvelib-1.0.1-py2.py3-none-any.whl (17 kB)
Downloading packaging-24.0-py3-none-any.whl (53 kB)
   ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 53.5/53.5 kB 4.9 MB/s eta 0:00:00
Downloading pycparser-2.21-py2.py3-none-any.whl (118 kB)
   ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 118.7/118.7 kB 7.3 MB/s eta 0:00:00
Installing collected packages: resolvelib, netaddr, ruamel.yaml.clib, pycparser, pbr, packaging, MarkupSafe, jmespath, importlib-resources, ruamel.yaml, jinja2, cffi, cryptography, ansible-core, ansible
  Attempting uninstall: MarkupSafe
    Found existing installation: MarkupSafe 1.1.0
    Uninstalling MarkupSafe-1.1.0:
      Successfully uninstalled MarkupSafe-1.1.0
  Attempting uninstall: jinja2
    Found existing installation: Jinja2 2.10.1
    Uninstalling Jinja2-2.10.1:
      Successfully uninstalled Jinja2-2.10.1
  Attempting uninstall: cryptography
    Found existing installation: cryptography 2.8
    Uninstalling cryptography-2.8:
      Successfully uninstalled cryptography-2.8
Successfully installed MarkupSafe-2.1.3 ansible-8.5.0 ansible-core-2.15.9 cffi-1.16.0 cryptography-41.0.4 importlib-resources-5.0.7 jinja2-3.1.2 jmespath-1.0.1 netaddr-0.9.0 packaging-24.0 pbr-5.11.1 pycparser-2.21 resolvelib-1.0.1 ruamel.yaml-0.17.35 ruamel.yaml.clib-0.2.8
WARNING: Running pip as the 'root' user can result in broken permissions and conflicting behaviour with the system package manager. It is recommended to use a virtual environment instead: https://pip.pypa.io/warnings/venv



~~~

Скачиваем инвенторку.

~~~
yc-user@masterk8s:~/kubespray$ sudo cp -rfp inventory/sample inventory/myclaster


~~~


Сделал конфиги hosts

~~~

yc-user@masterk8s:~/kubespray$ declare -a IPS=(158.160.100.171 158.160.118.2 158.160.52.9 158.160.122.32 178.154.203.94)


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
