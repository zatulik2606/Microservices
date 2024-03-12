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

...
~~~

VM создались в YC.

![vms](https://github.com/zatulik2606/Microservices/blob/main/installk8s/vms%20new5.jpg)


Скачиваю kubespray из репозитория


~~~
yc-user@masterk8s:~$ sudo git clone https://github.com/kubernetes-sigs/kubespray




~~~

Скачиваю нужную версию python.

~~~
sudo curl https://bootstrap.pypa.io/get-pip.py -o  get-pip.py
~~~

Запустили python 

~~~
c-user@masterk8s:~/kubespray$ python3.9 get-pip.py
Defaulting to user installation because normal site-packages is not writeable
Collecting pip
  Downloading pip-24.0-py3-none-any.whl.metadata (3.6 kB)
Downloading pip-24.0-py3-none-any.whl (2.1 MB)
   ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 2.1/2.1 MB 8.6 MB/s eta 0:00:00
Installing collected packages: pip
  WARNING: The scripts pip, pip3 and pip3.9 are installed in '/home/yc-user/.local/bin' which is not on PATH.
  Consider adding this directory to PATH or, if you prefer to suppress this warning, use --no-warn-script-location



yc-user@masterk8s:~/kubespray$ python3.9 get-pip.py --user
Collecting pip
  Using cached pip-24.0-py3-none-any.whl.metadata (3.6 kB)
Using cached pip-24.0-py3-none-any.whl (2.1 MB)
Installing collected packages: pip
  Attempting uninstall: pip
    Found existing installation: pip 24.0
    Uninstalling pip-24.0:
      Successfully uninstalled pip-24.0
  WARNING: The scripts pip, pip3 and pip3.9 are installed in '/home/yc-user/.local/bin' which is not on PATH.
  Consider adding this directory to PATH or, if you prefer to suppress this warning, use --no-warn-script-location.


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
   ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 47.5/47.5 MB 4.5 MB/s eta 0:00:00
Downloading cryptography-41.0.4-cp37-abi3-manylinux_2_28_x86_64.whl (4.4 MB)
   ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 4.4/4.4 MB 17.7 MB/s eta 0:00:00
Downloading Jinja2-3.1.2-py3-none-any.whl (133 kB)
   ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 133.1/133.1 kB 6.0 MB/s eta 0:00:00
Downloading jmespath-1.0.1-py3-none-any.whl (20 kB)
Downloading MarkupSafe-2.1.3-cp39-cp39-manylinux_2_17_x86_64.manylinux2014_x86_64.whl (25 kB)
Downloading netaddr-0.9.0-py3-none-any.whl (2.2 MB)
   ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 2.2/2.2 MB 77.0 MB/s eta 0:00:00
Downloading pbr-5.11.1-py2.py3-none-any.whl (112 kB)
   ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 112.7/112.7 kB 4.7 MB/s eta 0:00:00
Downloading ruamel.yaml-0.17.35-py3-none-any.whl (112 kB)
   ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 112.9/112.9 kB 7.0 MB/s eta 0:00:00
Downloading ruamel.yaml.clib-0.2.8-cp39-cp39-manylinux_2_5_x86_64.manylinux1_x86_64.whl (562 kB)
   ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 562.1/562.1 kB 42.9 MB/s eta 0:00:00
Downloading ansible_core-2.15.9-py3-none-any.whl (2.2 MB)
   ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 2.2/2.2 MB 62.2 MB/s eta 0:00:00
Downloading cffi-1.16.0-cp39-cp39-manylinux_2_17_x86_64.manylinux2014_x86_64.whl (443 kB)
   ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 443.4/443.4 kB 16.6 MB/s eta 0:00:00
Downloading importlib_resources-5.0.7-py3-none-any.whl (24 kB)
Downloading resolvelib-1.0.1-py2.py3-none-any.whl (17 kB)
Downloading packaging-24.0-py3-none-any.whl (53 kB)
   ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 53.5/53.5 kB 4.2 MB/s eta 0:00:00
Downloading pycparser-2.21-py2.py3-none-any.whl (118 kB)
   ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 118.7/118.7 kB 7.8 MB/s eta 0:00:00
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

yc-user@masterk8s:~/kubespray$ declare -a IPS=(158.160.117.149 51.250.65.195 158.160.36.239 158.160.56.10 158.160.57.13)


CONFIG_FILE=inventory/mycluster/hosts.yaml python3.9 contrib/inventory_builder/inventory.py ${IPS[@]}


yc-user@masterk8s:~/kubespray$ CONFIG_FILE=inventory/mycluster/hosts.yaml python3.9 contrib/inventory_builder/inventory.py ${IPS[@]}
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

Смотри м файл.

~~~
yc-user@masterk8s:~/kubespray/inventory/myclaster$ cat hosts.yaml
all:
  hosts:
    masterk8s:
      ansible_host: 158.160.117.149
      ip: 158.160.117.149
      access_ip: 158.160.117.149
      ansible_user: yc-user
    worker1:
      ansible_host: 51.250.65.195
      ip: 51.250.65.195
      access_ip: 51.250.65.195
      ansible_user: yc-user
    worker2:
      ansible_host: 158.160.56.10
      ip: 158.160.56.10
      access_ip: 158.160.56.10
      ansible_user: yc-user
    worker3:
      ansible_host: 158.160.57.13
      ip: 158.160.57.13
      access_ip: 158.160.57.13
      ansible_user: yc-user
    worker4:
      ansible_host: 158.160.36.239
      ip: 158.160.36.239
      access_ip: 158.160.36.239
      ansible_user: yc-user
  children:
    kube_control_plane:
      hosts:
        masterk8s:
    kube_node:
      hosts:
        worker1:
        worker2:
        worker3:
        worker4:
    etcd:
      hosts:
        masterk8s:
    k8s_cluster:
      children:
        kube_control_plane:
        kube_node:
    calico_rr:
      hosts: {}

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
