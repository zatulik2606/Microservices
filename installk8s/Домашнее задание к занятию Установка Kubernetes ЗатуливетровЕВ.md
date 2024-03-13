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




VM создались в YC.

~~~
root@debianz:~/.ssh# yc compute instance list
+----------------------+-----------+---------------+---------+-----------------+---------------+
|          ID          |   NAME    |    ZONE ID    | STATUS  |   EXTERNAL IP   |  INTERNAL IP  |
+----------------------+-----------+---------------+---------+-----------------+---------------+
| fhm5b5mo85fvntmk5rrg | ubuntu-hw | ru-central1-a | RUNNING | 158.160.38.134  | 192.168.10.7  |
| fhmdkpobefte18fl4fud | worker2   | ru-central1-a | RUNNING | 178.154.223.238 | 192.168.10.31 |
| fhmfb1lmdvg9nfftpbl2 | worker4   | ru-central1-a | RUNNING | 158.160.116.233 | 192.168.10.7  |
| fhmlfvdnv47blgkuedie | worker3   | ru-central1-a | RUNNING | 178.154.223.195 | 192.168.10.8  |
| fhmqp0skk2f22q6886lg | worker1   | ru-central1-a | RUNNING | 178.154.221.189 | 192.168.10.22 |
| fhmturslvorai3psjlnn | masterk8s | ru-central1-a | RUNNING | 84.201.174.48   | 192.168.10.4  |
+----------------------+-----------+---------------+---------+-----------------+---------------+


~~~


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






~~~

Скачиваем инвенторку.

~~~
yc-user@masterk8s:~/kubespray$ sudo cp -rfp inventory/sample inventory/mycluster


~~~


Сделал конфиги hosts

~~~
root@debianz:~/kubespray# declare -a IPS=(84.201.174.48 178.154.221.189 178.154.223.238 178.154.223.195 158.160.116.233)
root@debianz:~/kubespray# CONFIG_FILE=inventory/mycluster/hosts.yaml python3.9 contrib/inventory_builder/inventory.py ${IPS[@]}






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
root@debianz:~/kubespray# cat inventory/mycluster/hosts.yaml
all:
  hosts:
    masterk8s:
      ansible_host: 84.201.174.48
      ip: 84.201.174.48
      access_ip: 84.201.174.48
      ansible_user: yc-user  
    worker1:
      ansible_host: 178.154.221.189
      ip: 178.154.221.189
      access_ip: 178.154.221.189
      ansible_user: yc-user  
    worker2:
      ansible_host: 178.154.223.238
      ip: 178.154.223.238
      access_ip: 178.154.223.238
      ansible_user: yc-user  
    worker3:
      ansible_host: 178.154.223.195
      ip: 178.154.223.195
      access_ip: 178.154.223.195
      ansible_user: yc-user
    worker4:
      ansible_host: 158.160.116.233
      ip: 158.160.116.233
      access_ip: 158.160.116.233
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
