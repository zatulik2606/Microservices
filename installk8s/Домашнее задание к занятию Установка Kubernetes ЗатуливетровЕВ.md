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
| fhm47q0tbb734maa9q2s | node1     | ru-central1-a | RUNNING | 158.160.97.147  | 192.168.10.32 |
| fhm5b5mo85fvntmk5rrg | ubuntu-hw | ru-central1-a | RUNNING | 158.160.38.134  | 192.168.10.7  |
| fhm5ighrlepud7o3ta1g | node4     | ru-central1-a | RUNNING | 158.160.99.62   | 192.168.10.7  |
| fhmcgvvtimqt5lbrio05 | node3     | ru-central1-a | RUNNING | 178.154.207.17  | 192.168.10.27 |
| fhmlkiat7cvl3of8c3vc | node2     | ru-central1-a | RUNNING | 178.154.202.124 | 192.168.10.30 |
| fhmv61ne9uflserg7i8d | node5     | ru-central1-a | RUNNING | 51.250.75.152   | 192.168.10.37 |
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

root@debian:~/kubespray# sudo pip3.10 install -r requirements.txt
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
root@debian:~/kubespray$ sudo cp -rfp inventory/sample inventory/mycluster

~~~



Создал и заполнил вручную файл инвентори.

~~~
  root@debian:~# cat kubespray/inventory/inventory.ini
cat: kubespray/inventory/inventory.ini: Нет такого файла или каталога
root@debian:~# cat kubespray/inventory/mycluster/inventory.ini
# ## Configure 'ip' variable to bind kubernetes services on a
# ## different ip than the default iface
# ## We should set etcd_member_name for etcd cluster. The node that is not a etcd member do not need to set the value, or can set the empty string value.
[all]

node1 ansible_host=158.160.97.147 ansible_user=yc-user
node2 ansible_host=178.154.202.124 ansible_user=yc-user
node3 ansible_host=178.154.207.17 ansible_user=yc-user
node4 ansible_host=158.160.99.62 ansible_user=yc-user
node5 ansible_host=51.250.75.152 ansible_user=yc-user
 

# [bastion]
# bastion ansible_host=x.x.x.x ansible_user=some_user

[kube_control_plane]
node1

[etcd]
node1

[kube_node]
node2
node3
node4
node5


[calico_rr]

[k8s_cluster:children]
kube_control_plane
kube_node
calico_rr


~~~

Установил.

~~~
Пятница 15 марта 2024  10:08:12 +0300 (0:00:00.423)       0:23:25.558 ********* 

TASK [network_plugin/calico : Set calico_pool_conf] **********************************************************************************************************
ok: [node1] => {"ansible_facts": {"calico_pool_conf": {"apiVersion": "projectcalico.org/v3", "kind": "IPPool", "metadata": {"creationTimestamp": "2024-03-15T07:05:07Z", "name": "default-pool", "resourceVersion": "848", "uid": "ded5e869-e6d8-4341-9c66-a196c8493adf"}, "spec": {"allowedUses": ["Workload", "Tunnel"], "blockSize": 26, "cidr": "10.233.64.0/18", "ipipMode": "Never", "natOutgoing": true, "nodeSelector": "all()", "vxlanMode": "Always"}}}, "changed": false}
Пятница 15 марта 2024  10:08:12 +0300 (0:00:00.063)       0:23:25.621 ********* 

TASK [network_plugin/calico : Check if inventory match current cluster configuration] ************************************************************************
ok: [node1] => {
    "changed": false,
    "msg": "All assertions passed"
}
Пятница 15 марта 2024  10:08:12 +0300 (0:00:00.070)       0:23:25.691 ********* 
Пятница 15 марта 2024  10:08:12 +0300 (0:00:00.055)       0:23:25.747 ********* 
Пятница 15 марта 2024  10:08:13 +0300 (0:00:00.047)       0:23:25.794 ********* 

PLAY RECAP ***************************************************************************************************************************************************
node1                      : ok=697  changed=144  unreachable=0    failed=0    skipped=1170 rescued=0    ignored=6   
node2                      : ok=438  changed=88   unreachable=0    failed=0    skipped=697  rescued=0    ignored=1   
node3                      : ok=438  changed=88   unreachable=0    failed=0    skipped=693  rescued=0    ignored=1   
node4                      : ok=438  changed=88   unreachable=0    failed=0    skipped=693  rescued=0    ignored=1   
node5                      : ok=438  changed=88   unreachable=0    failed=0    skipped=693  rescued=0    ignored=1   
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
