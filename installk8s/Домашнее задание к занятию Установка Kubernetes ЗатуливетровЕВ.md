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

root@debian:~# yc compute instance list
+----------------------+-----------+---------------+---------+-----------------+---------------+
|          ID          |   NAME    |    ZONE ID    | STATUS  |   EXTERNAL IP   |  INTERNAL IP  |
+----------------------+-----------+---------------+---------+-----------------+---------------+
| fhm0pe873jn8fppivjfa | masterk8s | ru-central1-a | RUNNING | 158.160.124.90  | 192.168.10.17 |
| fhm5b5mo85fvntmk5rrg | ubuntu-hw | ru-central1-a | RUNNING | 158.160.38.134  | 192.168.10.7  |
| fhmgv3us1a18ddptgb5t | worker2   | ru-central1-a | RUNNING | 158.160.57.131  | 192.168.10.20 |
| fhmngehk2oid608vc6up | worker3   | ru-central1-a | RUNNING | 158.160.102.163 | 192.168.10.27 |
| fhmrl1pqpoqadj2g8gjd | worker4   | ru-central1-a | RUNNING | 158.160.126.206 | 192.168.10.30 |
| fhmro1n63ijk5uhjccov | worker1   | ru-central1-a | RUNNING | 158.160.104.64  | 192.168.10.4  |
+----------------------+-----------+---------------+---------+-----------------+---------------+


~~~ 


Скачиваю kubespray из репозитория


~~~
yc-user@masterk8s:~$ sudo git clone https://github.com/kubernetes-sigs/kubespray
Cloning into 'kubespray'...
remote: Enumerating objects: 73402, done.
remote: Counting objects: 100% (30/30), done.
remote: Compressing objects: 100% (26/26), done.
remote: Total 73402 (delta 8), reused 16 (delta 3), pack-reused 73372
Receiving objects: 100% (73402/73402), 23.26 MiB | 15.12 MiB/s, done.
Resolving deltas: 100% (41356/41356), done.





~~~


Устанавливаю зависимости.

~~~

yc-user@masterk8s:~/kubespray$ sudo pip3 install -r requirements.txt
Collecting ansible==8.5.0
  Downloading ansible-8.5.0-py3-none-any.whl (47.5 MB)
     |████████████████████████████████| 47.5 MB 25 kB/s 
Collecting cryptography==41.0.4
  Downloading cryptography-41.0.4-cp37-abi3-manylinux_2_17_x86_64.manylinux2014_x86_64.whl (4.4 MB)
     |████████████████████████████████| 4.4 MB 78 kB/s 
Collecting jinja2==3.1.2
  Downloading Jinja2-3.1.2-py3-none-any.whl (133 kB)
     |████████████████████████████████| 133 kB 60.2 MB/s 
Collecting jmespath==1.0.1
  Downloading jmespath-1.0.1-py3-none-any.whl (20 kB)
Collecting MarkupSafe==2.1.3
  Downloading MarkupSafe-2.1.3-cp39-cp39-manylinux_2_17_x86_64.manylinux2014_x86_64.whl (25 kB)
Collecting netaddr==0.9.0
  Downloading netaddr-0.9.0-py3-none-any.whl (2.2 MB)
     |████████████████████████████████| 2.2 MB 45.1 MB/s 
Collecting pbr==5.11.1
  Downloading pbr-5.11.1-py2.py3-none-any.whl (112 kB)
     |████████████████████████████████| 112 kB 63.7 MB/s 
Collecting ruamel.yaml==0.17.35
  Downloading ruamel.yaml-0.17.35-py3-none-any.whl (112 kB)
     |████████████████████████████████| 112 kB 802 bytes/s 
Collecting ruamel.yaml.clib==0.2.8
  Downloading ruamel.yaml.clib-0.2.8-cp39-cp39-manylinux_2_5_x86_64.manylinux1_x86_64.whl (562 kB)
     |████████████████████████████████| 562 kB 63.4 MB/s 
Collecting ansible-core~=2.15.5
  Downloading ansible_core-2.15.9-py3-none-any.whl (2.2 MB)
     |████████████████████████████████| 2.2 MB 40.3 MB/s 
Collecting cffi>=1.12
  Downloading cffi-1.16.0-cp39-cp39-manylinux_2_17_x86_64.manylinux2014_x86_64.whl (443 kB)
     |████████████████████████████████| 443 kB 65.1 MB/s 
Collecting resolvelib<1.1.0,>=0.5.3
  Downloading resolvelib-1.0.1-py2.py3-none-any.whl (17 kB)
Collecting importlib-resources<5.1,>=5.0; python_version < "3.10"
  Downloading importlib_resources-5.0.7-py3-none-any.whl (24 kB)
Requirement already satisfied: PyYAML>=5.1 in /usr/lib/python3/dist-packages (from ansible-core~=2.15.5->ansible==8.5.0->-r requirements.txt (line 1)) (5.3.1)
Collecting packaging
  Downloading packaging-24.0-py3-none-any.whl (53 kB)
     |████████████████████████████████| 53 kB 4.2 kB/s 
Collecting pycparser
  Downloading pycparser-2.21-py2.py3-none-any.whl (118 kB)
     |████████████████████████████████| 118 kB 64.8 MB/s 
Installing collected packages: resolvelib, importlib-resources, MarkupSafe, jinja2, pycparser, cffi, cryptography, packaging, ansible-core, ansible, jmespath, netaddr, pbr, ruamel.yaml.clib, ruamel.yaml
  Attempting uninstall: MarkupSafe
    Found existing installation: MarkupSafe 1.1.0
    Not uninstalling markupsafe at /usr/lib/python3/dist-packages, outside environment /usr
    Can't uninstall 'MarkupSafe'. No files were found to uninstall.
  Attempting uninstall: jinja2
    Found existing installation: Jinja2 2.10.1
    Not uninstalling jinja2 at /usr/lib/python3/dist-packages, outside environment /usr
    Can't uninstall 'Jinja2'. No files were found to uninstall.
  Attempting uninstall: cryptography
    Found existing installation: cryptography 2.8
    Not uninstalling cryptography at /usr/lib/python3/dist-packages, outside environment /usr
    Can't uninstall 'cryptography'. No files were found to uninstall.
Successfully installed MarkupSafe-2.1.3 ansible-8.5.0 ansible-core-2.15.9 cffi-1.16.0 cryptography-41.0.4 importlib-resources-5.0.7 jinja2-3.1.2 jmespath-1.0.1 netaddr-0.9.0 packaging-24.0 pbr-5.11.1 pycparser-2.21 resolvelib-1.0.1 ruamel.yaml-0.17.35 ruamel.yaml.clib-0.2.8

Скачиваем инвенторку.

~~~
yc-user@masterk8s:~/kubespray$ sudo cp -rfp inventory/sample inventory/mycluster


~~~


Сделал конфиги hosts

~~~


yc-user@masterk8s:~/kubespray$ declare -a IPS=(158.160.124.90 158.160.104.64 158.160.57.131 158.160.102.163 158.160.126.206)
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

Смотри м файл.

~~~

yc-user@masterk8s:~/kubespray$ cat inventory/mycluster/hosts.yaml
all:
  hosts:
    masterk8s:
      ansible_host: 158.160.124.90
      ip: 158.160.124.90
      access_ip: 158.160.124.90
      ansible_user: yc-user
    worker1:
      ansible_host: 158.160.104.64
      ip: 158.160.104.64
      access_ip: 158.160.104.64
      ansible-user: yc-user
    worker2:
      ansible_host: 158.160.57.131
      ip: 158.160.57.131
      access_ip: 158.160.57.131
      ansible_user: yc-user
    worker3:
      ansible_host: 158.160.102.163
      ip: 158.160.102.163
      access_ip: 158.160.102.163
      ansible_user: yc-user
    worker4:
      ansible_host: 158.160.126.206
      ip: 158.160.126.206
      access_ip: 158.160.126.206
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
