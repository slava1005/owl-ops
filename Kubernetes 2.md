# Домашнее задание к занятию «Kubernetes. Часть 2»
### Задание 1

**Выполните действия:**

1. Создайте свой кластер с помощью kubeadm.
1. Установите любой понравившийся CNI плагин.
1. Добейтесь стабильной работы кластера.

В качестве ответа пришлите скриншот результата выполнения команды `kubectl get po -n kube-system`.

![2023-07-21_11-22-26](https://github.com/slava1005/owl-ops/assets/114395964/98946e75-57fb-466f-adb0-91e74675ce9d)

---

### Задание 2

Есть файл с деплоем:

```yaml
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: redis
spec:
  selector:
    matchLabels:
      app: redis
  replicas: 1
  template:
    metadata:
      labels:
        app: redis
    spec:
      containers:
      - name: master
        image: bitnami/redis
        env:
         - name: REDIS_PASSWORD
           value: password123
        ports:
        - containerPort: 6379
```
**Выполните действия:**

1. Создайте Helm Charts.
1. Добавьте в него сервис.
1. Вынесите все нужные, на ваш взгляд, параметры в `values.yaml`.
1. Запустите чарт в своём кластере и добейтесь его стабильной работы.

В качестве ответа пришлите вывод команды `helm get manifest <имя_релиза>`.

![2023-07-21_11-23-17](https://github.com/slava1005/owl-ops/assets/114395964/1c4644ea-48b2-4777-9c27-d1d3d8be4d5e)

```
---
# Source: redis/templates/serviceaccount.yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: redis
  labels:
    helm.sh/chart: redis-0.1.0
    app.kubernetes.io/name: redis
    app.kubernetes.io/instance: redis
    app.kubernetes.io/managed-by: Helm
---
# Source: redis/templates/deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: redis
spec:
  selector:
    matchLabels:
      app: redis
  replicas: 1
  template:
    metadata:
      labels:
        app: redis
    spec:
      containers:
      - name: master
        image: bitnami/redis
        env:
        - name: REDIS_PASSWORD
          value: password12
        environment:
          - ALLOW_EMPTY_PASSWORD=yes           
        ports:
        - containerPort: 6379
        
---
# Source: redis/templates/service.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: redis
  labels:
    app: redis
spec:
  replicas: 1
  selector:
    matchLabels:
      app: redis
  template:
    metadata:
      labels:
        app: redis
    spec:
      containers:
        - name: redis
          image: "bitnami/redis:latest"
          environment:
            - ALLOW_EMPTY_PASSWORD=yes
          ports:
            - containerPort: 6379


```
