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
| fhm5b5mo85fvntmk5rrg | ubuntu-hw | ru-central1-a | RUNNING | 158.160.38.134  | 192.168.10.7  |
| fhm9l9oq4pjbs9uq6pi3 | worker4   | ru-central1-a | RUNNING | 158.160.46.59   | 192.168.10.25 |
| fhmk5j833fn7snd8k94g | worker2   | ru-central1-a | RUNNING | 178.154.221.7   | 192.168.10.16 |
| fhmmsonfhumutmgjbi43 | masterk8s | ru-central1-a | RUNNING | 51.250.88.222   | 192.168.10.17 |
| fhmt2av42feeivm0oghv | worker1   | ru-central1-a | RUNNING | 158.160.32.65   | 192.168.10.8  |
| fhmu10jgttgpuo9st2hs | worker3   | ru-central1-a | RUNNING | 178.154.202.134 | 192.168.10.30 |
+----------------------+-----------+---------------+---------+-----------------+---------------+


~~~ 


Скачиваю kubespray из репозитория


~~~
root@debian:~# git clone https://github.com/kubernetes-sigs/kubespray
Клонирование в «kubespray»...
remote: Enumerating objects: 73402, done.
remote: Counting objects: 100% (29/29), done.
remote: Compressing objects: 100% (25/25), done.
remote: Total 73402 (delta 8), reused 16 (delta 3), pack-reused 73373
Получение объектов: 100% (73402/73402), 23.16 МиБ | 1.92 МиБ/с, готово.
Определение изменений: 100% (41348/41348), готово.




~~~


Устанавливаю зависимости.

~~~

root@debian:~/kubespray# pip3.11 install -r requirements.txt
Requirement already satisfied: ansible==8.5.0 in /usr/local/lib/python3.11/dist-packages (from -r requirements.txt (line 1)) (8.5.0)
Requirement already satisfied: cryptography==41.0.4 in /usr/local/lib/python3.11/dist-packages (from -r requirements.txt (line 2)) (41.0.4)
Requirement already satisfied: jinja2==3.1.2 in /usr/lib/python3/dist-packages (from -r requirements.txt (line 3)) (3.1.2)
Requirement already satisfied: jmespath==1.0.1 in /usr/lib/python3/dist-packages (from -r requirements.txt (line 4)) (1.0.1)
Requirement already satisfied: MarkupSafe==2.1.3 in /usr/local/lib/python3.11/dist-packages (from -r requirements.txt (line 5)) (2.1.3)
Requirement already satisfied: netaddr==0.9.0 in /usr/local/lib/python3.11/dist-packages (from -r requirements.txt (line 6)) (0.9.0)
Requirement already satisfied: pbr==5.11.1 in /usr/local/lib/python3.11/dist-packages (from -r requirements.txt (line 7)) (5.11.1)
Requirement already satisfied: ruamel.yaml==0.17.35 in /usr/local/lib/python3.11/dist-packages (from -r requirements.txt (line 8)) (0.17.35)
Requirement already satisfied: ruamel.yaml.clib==0.2.8 in /usr/local/lib/python3.11/dist-packages (from -r requirements.txt (line 9)) (0.2.8)
Requirement already satisfied: ansible-core~=2.15.5 in /usr/local/lib/python3.11/dist-packages (from ansible==8.5.0->-r requirements.txt (line 1)) (2.15.5)
Requirement already satisfied: cffi>=1.12 in /usr/local/lib/python3.11/dist-packages (from cryptography==41.0.4->-r requirements.txt (line 2)) (1.16.0)
Requirement already satisfied: PyYAML>=5.1 in /usr/lib/python3/dist-packages (from ansible-core~=2.15.5->ansible==8.5.0->-r requirements.txt (line 1)) (6.0)
Requirement already satisfied: packaging in /usr/lib/python3/dist-packages (from ansible-core~=2.15.5->ansible==8.5.0->-r requirements.txt (line 1)) (23.0)
Requirement already satisfied: resolvelib<1.1.0,>=0.5.3 in /usr/lib/python3/dist-packages (from ansible-core~=2.15.5->ansible==8.5.0->-r requirements.txt (line 1)) (0.9.0)
Requirement already satisfied: pycparser in /usr/local/lib/python3.11/dist-packages (from cffi>=1.12->cryptography==41.0.4->-r requirements.txt (line 2)) (2.21)
WARNING: Running pip as the 'root' user can result in broken permissions and conflicting behaviour with the system package manager. It is recommended to use a virtual environment instead: https://pip.pypa.io/warnings/venv
root@debian:~/kubespray# 



~~~

Скачиваем инвенторку.

~~~
yc-user@masterk8s:~/kubespray$ sudo cp -rfp inventory/sample inventory/mycluster


~~~


Сделал конфиги hosts

~~~


root@debian:~/kubespray# declare -a IPS=(51.250.88.222 158.160.32.65 178.154.221.7 78.154.202.134 158.160.46.59)
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
      ansible_host: 51.250.88.222
      ip: 51.250.88.222
      access_ip: 51.250.88.222
      ansible_user: yc-user  
    worker1:
      ansible_host: 158.160.32.65
      ip: 158.160.32.65
      access_ip: 158.160.32.65
      ansible_user: yc-user  
    worker2:
      ansible_host: 178.154.221.7
      ip: 178.154.221.7
      access_ip: 178.154.221.7
      ansible_user: yc-user  
    worker3:
      ansible_host: 78.154.202.134
      ip: 78.154.202.134
      access_ip: 78.154.202.134
      ansible_user: yc-user  
    worker4:
      ansible_host: 158.160.46.59
      ip: 158.160.46.59
      access_ip: 158.160.46.59
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
