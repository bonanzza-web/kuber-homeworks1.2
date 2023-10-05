# Домашнее задание к занятию «Базовые объекты K8S»

### Цель задания

В тестовой среде для работы с Kubernetes, установленной в предыдущем ДЗ, необходимо развернуть Pod с приложением и подключиться к нему со своего локального компьютера. 

------

### Чеклист готовности к домашнему заданию

1. Установленное k8s-решение (например, MicroK8S).
2. Установленный локальный kubectl.
3. Редактор YAML-файлов с подключенным Git-репозиторием.

------

### Инструменты и дополнительные материалы, которые пригодятся для выполнения задания

1. Описание [Pod](https://kubernetes.io/docs/concepts/workloads/pods/) и примеры манифестов.
2. Описание [Service](https://kubernetes.io/docs/concepts/services-networking/service/).

------

### Задание 1. Создать Pod с именем hello-world

1. Создать манифест (yaml-конфигурацию) Pod.
2. Использовать image - gcr.io/kubernetes-e2e-test-images/echoserver:2.2.
3. Подключиться локально к Pod с помощью `kubectl port-forward` и вывести значение (curl или в браузере).


## Ответы:    

```
apiVersion: v1
kind: Pod
metadata:
  labels:
    app: hello
  name: hello-world

spec:
  containers:
  - image: gcr.io/kubernetes-e2e-test-images/echoserver:2.2
    name: hello-world

```

```
ubuntu@server-1:~/cluster$ micro apply -f hello-world.yaml
pod/hello-world created
ubuntu@server-1:~/cluster$ micro get pods
NAME          READY   STATUS    RESTARTS   AGE
hello-world   1/1     Running   0          9s

```

```
ubuntu@server-1:~$ sudo microk8s kubectl port-forward hello-world 80:8080
Forwarding from 127.0.0.1:80 -> 8080
Forwarding from [::1]:80 -> 8080
Handling connection for 80

```

```
ubuntu@server-1:~/cluster$ curl localhost


Hostname: hello-world

Pod Information:
        -no pod information available-

Server values:
        server_version=nginx: 1.12.2 - lua: 10010

Request Information:
        client_address=127.0.0.1
        method=GET
        real path=/
        query=
        request_version=1.1
        request_scheme=http
        request_uri=http://localhost:8080/

Request Headers:
        accept=*/*
        host=localhost
        user-agent=curl/7.81.0

Request Body:
        -no body in request-

ubuntu@server-1:~/cluster$

```


------

### Задание 2. Создать Service и подключить его к Pod

1. Создать Pod с именем netology-web.
2. Использовать image — gcr.io/kubernetes-e2e-test-images/echoserver:2.2.
3. Создать Service с именем netology-svc и подключить к netology-web.
4. Подключиться локально к Service с помощью `kubectl port-forward` и вывести значение (curl или в браузере).


## Ответы:    

```
apiVersion: v1
kind: Pod
metadata:
  labels:
    app: netology
  name: netology-web

spec:
  containers:
  - name: netology-web
    image: gcr.io/kubernetes-e2e-test-images/echoserver:2.2

```
```
apiVersion: v1
kind: Service
metadata:
  name: netology-svc
spec:
  selector:
    app: netology
  ports:
    - protocol: TCP
      port: 80
      targetPort: 8080

```
```
ubuntu@server-1:~/cluster$ micro apply -f netology-web.yaml
pod/netology-web created
ubuntu@server-1:~/cluster$ micro apply -f netology-svc.yaml
service/netology-svc created
ubuntu@server-1:~/cluster$ micro get all
NAME               READY   STATUS    RESTARTS   AGE
pod/hello-world    1/1     Running   0          13m
pod/netology-web   1/1     Running   0          3m2s

NAME                   TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)   AGE
service/kubernetes     ClusterIP   10.152.183.1     <none>        443/TCP   32m
service/netology-svc   ClusterIP   10.152.183.143   <none>        80/TCP    91s
```

```
ubuntu@server-1:~$ sudo microk8s kubectl port-forward svc/netology-svc 80:80
Forwarding from 127.0.0.1:80 -> 8080
Forwarding from [::1]:80 -> 8080
Handling connection for 80

```

```
ubuntu@server-1:~/cluster$ curl localhost


Hostname: netology-web

Pod Information:
        -no pod information available-

Server values:
        server_version=nginx: 1.12.2 - lua: 10010

Request Information:
        client_address=127.0.0.1
        method=GET
        real path=/
        query=
        request_version=1.1
        request_scheme=http
        request_uri=http://localhost:8080/

Request Headers:
        accept=*/*
        host=localhost
        user-agent=curl/7.81.0

Request Body:
        -no body in request-

```


------

### Правила приёма работы

1. Домашняя работа оформляется в своем Git-репозитории в файле README.md. Выполненное домашнее задание пришлите ссылкой на .md-файл в вашем репозитории.
2. Файл README.md должен содержать скриншоты вывода команд `kubectl get pods`, а также скриншот результата подключения.
3. Репозиторий должен содержать файлы манифестов и ссылки на них в файле README.md.

------

### Критерии оценки
Зачёт — выполнены все задания, ответы даны в развернутой форме, приложены соответствующие скриншоты и файлы проекта, в выполненных заданиях нет противоречий и нарушения логики.

На доработку — задание выполнено частично или не выполнено, в логике выполнения заданий есть противоречия, существенные недостатки.
