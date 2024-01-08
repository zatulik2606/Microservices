# Домашнее задание к занятию «Базовые объекты K8S»

### Цель задания


### Задание 1. Создать Pod с именем hello-world

1. Создать манифест (yaml-конфигурацию) Pod.
2. Использовать image - gcr.io/kubernetes-e2e-test-images/echoserver:2.2.
3. Подключиться локально к Pod с помощью `kubectl port-forward` и вывести значение (curl или в браузере).


Создал и подключился в браузере.

![Hello_world](https://github.com/zatulik2606/Microservices/blob/scerenshorts/hello-world1.jpg)

------

### Задание 2. Создать Service и подключить его к Pod

1. Создать Pod с именем netology-web.
2. Использовать image — gcr.io/kubernetes-e2e-test-images/echoserver:2.2.
3. Создать Service с именем netology-svc и подключить к netology-web.
4. Подключиться локально к Service с помощью `kubectl port-forward` и вывести значение (curl или в браузере).


Операция по перебросу портов выполнялась успешно, но при подключении через curl появляалсь ошибка в виде "curl: (52) Empty reply from server".


Добавил в конфигурацию еще один образ.


![Netology-web](https://github.com/zatulik2606/Microservices/blob/scerenshorts/configpodnetologyweb.png)



Так выглядит сервис.

![Netology-svc](https://github.com/zatulik2606/Microservices/blob/scerenshorts/configpodnetologysvc.png)


Так смотрим pods и svc.

![getpodssvc](https://github.com/zatulik2606/Microservices/blob/scerenshorts/kubectlgetpods.png)


Запускаем переброс портов.


![portfw](https://github.com/zatulik2606/Microservices/blob/scerenshorts/porforward.png)


Смотрим вывод curl.


![curl](https://github.com/zatulik2606/Microservices/blob/scerenshorts/curl.png)

------

### Правила приёма работы

1. Домашняя работа оформляется в своем Git-репозитории в файле README.md. Выполненное домашнее задание пришлите ссылкой на .md-файл в вашем репозитории.
2. Файл README.md должен содержать скриншоты вывода команд `kubectl get pods`, а также скриншот результата подключения.
3. Репозиторий должен содержать файлы манифестов и ссылки на них в файле README.md.

------

### Критерии оценки
Зачёт — выполнены все задания, ответы даны в развернутой форме, приложены соответствующие скриншоты и файлы проекта, в выполненных заданиях нет противоречий и нарушения логики.

На доработку — задание выполнено частично или не выполнено, в логике выполнения заданий есть противоречия, существенные недостатки.
