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

root@debian:~# yc compute i\nstance list
+----------------------+-----------+---------------+---------+-----------------+---------------+
|          ID          |   NAME    |    ZONE ID    | STATUS  |   EXTERNAL IP   |  INTERNAL IP  |
+----------------------+-----------+---------------+---------+-----------------+---------------+
| fhm4179dp06tsc1ifngj | worker2   | ru-central1-a | RUNNING | 84.201.135.38   | 192.168.10.28 |
| fhm5b5mo85fvntmk5rrg | ubuntu-hw | ru-central1-a | RUNNING | 158.160.38.134  | 192.168.10.7  |
| fhmarrcdnd1aas2ven5k | worker3   | ru-central1-a | RUNNING | 178.154.204.250 | 192.168.10.31 |
| fhmeg55n7s5j1do95q0r | worker4   | ru-central1-a | RUNNING | 178.154.222.12  | 192.168.10.19 |
| fhmjqmea3tb063adea1k | masterk8s | ru-central1-a | RUNNING | 51.250.2.16     | 192.168.10.16 |
| fhmo6cm30ghe5or90ra8 | worker1   | ru-central1-a | RUNNING | 158.160.117.143 | 192.168.10.25 |
+----------------------+-----------+---------------+---------+-----------------+---------------+

~~~ 


Скачиваю kubespray из репозитория


~~~
root@debian:~# git clone https://github.com/kubernetes-sigs/kubespray
Клонирование в «kubespray»...
remote: Enumerating objects: 73402, done.
remote: Counting objects: 100% (30/30), done.
remote: Compressing objects: 100% (26/26), done.
remote: Total 73402 (delta 8), reused 16 (delta 3), pack-reused 73372
Получение объектов: 100% (73402/73402), 23.26 МиБ | 3.35 МиБ/с, готово.
Определение изменений: 100% (41356/41356), готово.
root@debian:~# cd kubespray
root@debian:~/kubespray# 




~~~


Устанавливаю зависимости.

~~~

root@debian:~/kubespray# pip3 --version
pip 23.0.1 from /usr/lib/python3/dist-packages/pip (python 3.11)
root@debian:~/kubespray# 
root@debian:~/kubespray#  pip3.11 install -r requirements.txt
Requirement already satisfied: ansible==8.5.0 in /usr/local/lib/python3.11/dist-packages (from -r requirements.txt (line 1)) (8.5.0)
Collecting cryptography==41.0.4
  Using cached cryptography-41.0.4-cp37-abi3-manylinux_2_28_x86_64.whl (4.4 MB)
Requirement already satisfied: jinja2==3.1.2 in /usr/lib/python3/dist-packages (from -r requirements.txt (line 3)) (3.1.2)
Requirement already satisfied: jmespath==1.0.1 in /usr/lib/python3/dist-packages (from -r requirements.txt (line 4)) (1.0.1)
Collecting MarkupSafe==2.1.3
  Using cached MarkupSafe-2.1.3-cp311-cp311-manylinux_2_17_x86_64.manylinux2014_x86_64.whl (28 kB)
Collecting netaddr==0.9.0
  Downloading netaddr-0.9.0-py3-none-any.whl (2.2 MB)
     ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 2.2/2.2 MB 3.6 MB/s eta 0:00:00
Collecting pbr==5.11.1
  Downloading pbr-5.11.1-py2.py3-none-any.whl (112 kB)
     ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 112.7/112.7 kB 5.3 MB/s eta 0:00:00
Collecting ruamel.yaml==0.17.35
  Downloading ruamel.yaml-0.17.35-py3-none-any.whl (112 kB)
     ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 112.9/112.9 kB 7.7 MB/s eta 0:00:00
Collecting ruamel.yaml.clib==0.2.8
  Downloading ruamel.yaml.clib-0.2.8-cp311-cp311-manylinux_2_17_x86_64.manylinux2014_x86_64.manylinux_2_24_x86_64.whl (544 kB)
     ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 544.0/544.0 kB 4.6 MB/s eta 0:00:00
Requirement already satisfied: ansible-core~=2.15.5 in /usr/local/lib/python3.11/dist-packages (from ansible==8.5.0->-r requirements.txt (line 1)) (2.15.5)
Collecting cffi>=1.12
  Using cached cffi-1.16.0-cp311-cp311-manylinux_2_17_x86_64.manylinux2014_x86_64.whl (464 kB)
Requirement already satisfied: PyYAML>=5.1 in /usr/lib/python3/dist-packages (from ansible-core~=2.15.5->ansible==8.5.0->-r requirements.txt (line 1)) (6.0)
Requirement already satisfied: packaging in /usr/lib/python3/dist-packages (from ansible-core~=2.15.5->ansible==8.5.0->-r requirements.txt (line 1)) (23.0)
Requirement already satisfied: resolvelib<1.1.0,>=0.5.3 in /usr/lib/python3/dist-packages (from ansible-core~=2.15.5->ansible==8.5.0->-r requirements.txt (line 1)) (0.9.0)
Collecting pycparser
  Using cached pycparser-2.21-py2.py3-none-any.whl (118 kB)
Installing collected packages: netaddr, ruamel.yaml.clib, pycparser, pbr, MarkupSafe, ruamel.yaml, cffi, cryptography
  Attempting uninstall: netaddr
    Found existing installation: netaddr 0.8.0
    Not uninstalling netaddr at /usr/lib/python3/dist-packages, outside environment /usr
    Can't uninstall 'netaddr'. No files were found to uninstall.
  Attempting uninstall: ruamel.yaml.clib
    Found existing installation: ruamel.yaml.clib 0.2.7
    Not uninstalling ruamel-yaml-clib at /usr/lib/python3/dist-packages, outside environment /usr
    Can't uninstall 'ruamel.yaml.clib'. No files were found to uninstall.
  Attempting uninstall: pbr
    Found existing installation: pbr 5.10.0
    Not uninstalling pbr at /usr/lib/python3/dist-packages, outside environment /usr
    Can't uninstall 'pbr'. No files were found to uninstall.
  Attempting uninstall: MarkupSafe
    Found existing installation: MarkupSafe 2.1.2
    Not uninstalling markupsafe at /usr/lib/python3/dist-packages, outside environment /usr
    Can't uninstall 'MarkupSafe'. No files were found to uninstall.
  Attempting uninstall: ruamel.yaml
    Found existing installation: ruamel.yaml 0.17.21
    Not uninstalling ruamel-yaml at /usr/lib/python3/dist-packages, outside environment /usr
    Can't uninstall 'ruamel.yaml'. No files were found to uninstall.
  Attempting uninstall: cryptography
    Found existing installation: cryptography 38.0.4
    Not uninstalling cryptography at /usr/lib/python3/dist-packages, outside environment /usr
    Can't uninstall 'cryptography'. No files were found to uninstall.
Successfully installed MarkupSafe-2.1.3 cffi-1.16.0 cryptography-41.0.4 netaddr-0.9.0 pbr-5.11.1 pycparser-2.21 ruamel.yaml-0.17.35 ruamel.yaml.clib-0.2.8
WARNING: Running pip as the 'root' user can result in broken permissions and conflicting behaviour with the system package manager. It is recommended to use a virtual environment instead: https://pip.pypa.io/warnings/venv
root@debian:~/kubespray# pip3 --version
pip 23.0.1 from /usr/lib/python3/dist-packages/pip (python 3.11)







~~~

Скачиваем инвенторку.

~~~
yc-user@masterk8s:~/kubespray$ sudo cp -rfp inventory/sample inventory/mycluster


~~~


Сделал конфиги hosts

~~~
root@debian:~/kubespray# cp -rfp inventory/sample inventory/mycluster
root@debian:~/kubespray# declare -a IPS=(51.250.2.16 158.160.117.143 84.201.135.38 178.154.204.250 178.154.222.12)
root@debian:~/kubespray# CONFIG_FILE=inventory/mycluster/hosts.yaml python3.11 contrib/inventory_builder/inventory.py ${IPS[@]}
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
root@debian:~/kubespray# cat inventory/mycluster/hosts.yaml
all:
  hosts:
    masterk8s:
      ansible_host: 51.250.2.16
      ip: 51.250.2.16
      access_ip: 51.250.2.16
      ansible_user: yc-user  
    worker1:
      ansible_host: 158.160.117.143
      ip: 158.160.117.143
      access_ip: 158.160.117.143
      ansible_user: yc-user  
    worker2:
      ansible_host: 84.201.135.38
      ip: 84.201.135.38
      access_ip: 84.201.135.38
      ansible_user: yc-user  
    worker3:
      ansible_host: 178.154.204.250
      ip: 178.154.204.250
      access_ip: 178.154.204.250
      ansible_user: yc-user  
    worker4:
      ansible_host: 178.154.222.12
      ip: 178.154.222.12
      access_ip: 178.154.222.12
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
