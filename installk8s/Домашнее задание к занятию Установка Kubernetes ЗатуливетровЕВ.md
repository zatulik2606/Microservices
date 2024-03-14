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



VM создались в YC.

~~~

root@debian:~/mvs# yc compute instance list
+----------------------+-----------+---------------+---------+-----------------+---------------+
|          ID          |   NAME    |    ZONE ID    | STATUS  |   EXTERNAL IP   |  INTERNAL IP  |
+----------------------+-----------+---------------+---------+-----------------+---------------+
| fhm12ddv7d0l83o4kq3n | worker2   | ru-central1-a | RUNNING | 178.154.202.182 | 192.168.10.5  |
| fhm5b5mo85fvntmk5rrg | ubuntu-hw | ru-central1-a | RUNNING | 158.160.38.134  | 192.168.10.7  |
| fhmceb48e286afks8j7a | worker3   | ru-central1-a | RUNNING | 178.154.222.118 | 192.168.10.25 |
| fhmfvjsddde9es6uqjm9 | masterk8s | ru-central1-a | RUNNING | 158.160.125.11  | 192.168.10.34 |
| fhmq7e8ei6j5ss6h52oq | worker4   | ru-central1-a | RUNNING | 178.154.222.30  | 192.168.10.8  |
| fhmt1pafqkl1vn64nvbe | worker1   | ru-central1-a | RUNNING | 158.160.52.54   | 192.168.10.23 |
+----------------------+-----------+---------------+---------+-----------------+---------------+



~~~ 


Скачиваю kubespray из репозитория


~~~
root@debian:~# git clone https://github.com/kubernetes-sigs/kubespray
Клонирование в «kubespray»...
remote: Enumerating objects: 73424, done.
remote: Counting objects: 100% (38/38), done.
remote: Compressing objects: 100% (30/30), done.
remote: Total 73424 (delta 12), reused 22 (delta 7), pack-reused 73386
Получение объектов: 100% (73424/73424), 23.27 МиБ | 7.92 МиБ/с, готово.
Определение изменений: 100% (41372/41372), готово.


~~~


Устанавливаю зависимости.

~~~

root@debian:~/kubespray# sudo pip3.11 install -r requirements.txt
Collecting ansible==9.3.0
  Downloading ansible-9.3.0-py3-none-any.whl (46.3 MB)
     ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 46.3/46.3 MB 9.0 MB/s eta 0:00:00
Requirement already satisfied: cryptography==41.0.4 in /usr/local/lib/python3.11/dist-packages (from -r requirements.txt (line 2)) (41.0.4)
Requirement already satisfied: jinja2==3.1.2 in /usr/lib/python3/dist-packages (from -r requirements.txt (line 3)) (3.1.2)
Requirement already satisfied: jmespath==1.0.1 in /usr/lib/python3/dist-packages (from -r requirements.txt (line 4)) (1.0.1)
Requirement already satisfied: MarkupSafe==2.1.3 in /usr/local/lib/python3.11/dist-packages (from -r requirements.txt (line 5)) (2.1.3)
Requirement already satisfied: netaddr==0.9.0 in /usr/local/lib/python3.11/dist-packages (from -r requirements.txt (line 6)) (0.9.0)
Requirement already satisfied: pbr==5.11.1 in /usr/local/lib/python3.11/dist-packages (from -r requirements.txt (line 7)) (5.11.1)
Collecting ruamel.yaml==0.18.5
  Downloading ruamel.yaml-0.18.5-py3-none-any.whl (116 kB)
     ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 116.4/116.4 kB 16.7 MB/s eta 0:00:00
Requirement already satisfied: ruamel.yaml.clib==0.2.8 in /usr/local/lib/python3.11/dist-packages (from -r requirements.txt (line 9)) (0.2.8)
Collecting ansible-core~=2.16.4
  Downloading ansible_core-2.16.4-py3-none-any.whl (2.3 MB)
     ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 2.3/2.3 MB 12.3 MB/s eta 0:00:00
Requirement already satisfied: cffi>=1.12 in /usr/local/lib/python3.11/dist-packages (from cryptography==41.0.4->-r requirements.txt (line 2)) (1.16.0)
Requirement already satisfied: PyYAML>=5.1 in /usr/lib/python3/dist-packages (from ansible-core~=2.16.4->ansible==9.3.0->-r requirements.txt (line 1)) (6.0)
Requirement already satisfied: packaging in /usr/lib/python3/dist-packages (from ansible-core~=2.16.4->ansible==9.3.0->-r requirements.txt (line 1)) (23.0)
Requirement already satisfied: resolvelib<1.1.0,>=0.5.3 in /usr/lib/python3/dist-packages (from ansible-core~=2.16.4->ansible==9.3.0->-r requirements.txt (line 1)) (0.9.0)
Requirement already satisfied: pycparser in /usr/local/lib/python3.11/dist-packages (from cffi>=1.12->cryptography==41.0.4->-r requirements.txt (line 2)) (2.21)
Installing collected packages: ruamel.yaml, ansible-core, ansible
  Attempting uninstall: ruamel.yaml
    Found existing installation: ruamel.yaml 0.17.35
    Uninstalling ruamel.yaml-0.17.35:
      Successfully uninstalled ruamel.yaml-0.17.35
  Attempting uninstall: ansible-core
    Found existing installation: ansible-core 2.15.5
    Uninstalling ansible-core-2.15.5:
      Successfully uninstalled ansible-core-2.15.5
  Attempting uninstall: ansible
    Found existing installation: ansible 8.5.0
    Uninstalling ansible-8.5.0:
      Successfully uninstalled ansible-8.5.0
ERROR: pip's dependency resolver does not currently take into account all the packages that are installed. This behaviour is the source of the following dependency conflicts.
ansible-lint 6.21.1 requires ruamel.yaml!=0.17.29,!=0.17.30,<0.18,>=0.17.0, but you have ruamel-yaml 0.18.5 which is incompatible.
Successfully installed ansible-9.3.0 ansible-core-2.16.4 ruamel.yaml-0.18.5
WARNING: Running pip as the 'root' user can result in broken permissions and conflicting behaviour with the system package manager. It is recommended to use a virtual environment instead: https://pip.pypa.io/warnings/venv

~~~
Скачиваем инвенторку.

~~~
yc-user@masterk8s:~/kubespray$ sudo cp -rfp inventory/sample inventory/mycluster

~~~


Сделал конфиги hosts

~~~

yc-user@masterk8s:~/kubespray$ declare -a IPS=(158.160.122.255 158.160.44.189 51.250.13.73 178.154.205.143 178.154.206.96)
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


Смотрим файл.

~~~
yc-user@masterk8s:~/kubespray$ cat inventory/mycluster/hosts.yaml
all:
  hosts:
    masterk8s:
      ansible_host: 158.160.122.255
      ip: 158.160.122.255
      access_ip: 158.160.122.255
      ansible_user: yc-user
      ansible_ssh_private_key_file: ~/.ssh/id_rsa
    wokker1:
      ansible_host: 158.160.44.189
      ip: 158.160.44.189
      access_ip: 158.160.44.189
      ansible_user: yc-user
      ansible_ssh_private_key_file: ~/.ssh/id_rsa
    worker2:
      ansible_host: 51.250.13.73
      ip: 51.250.13.73
      access_ip: 51.250.13.73
      ansible_user: yc-user
      ansible_ssh_private_key_file: ~/.ssh/id_rsa
    worker3:
      ansible_host: 178.154.205.143
      ip: 178.154.205.143
      access_ip: 178.154.205.143
      ansible_user: yc-user
      ansible_ssh_private_key_file: ~/.ssh/id_rsa
    worker4:
      ansible_host: 178.154.206.96
      ip: 178.154.206.96
      access_ip: 178.154.206.96
      ansible_user: yc-user
      ansible_ssh_private_key_file: ~/.ssh/id_rsa
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
