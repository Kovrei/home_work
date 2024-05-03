# Домашнее задание к занятию «Kubernetes. Часть 1»

# Задание 1
Выполните действия:  

Запустите Kubernetes локально, используя k3s или minikube на свой выбор.  
Добейтесь стабильной работы всех системных контейнеров.  
# Задание 2
Есть файл с деплоем:
```java
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
Выполните действия:  

Измените файл с учётом условий:  
redis должен запускаться без пароля;  
создайте Service, который будет направлять трафик на этот Deployment;  
версия образа redis должна быть зафиксирована на 6.0.13.  
Запустите Deployment в своём кластере и добейтесь его стабильной работы.  
# Задание 3 
Выполните действия:  

Напишите команды kubectl для контейнера из предыдущего задания:  
выполнения команды ps aux внутри контейнера;  
просмотра логов контейнера за последние 5 минут;  
удаления контейнера;  
проброса порта локальной машины в контейнер для отладки.  
# Задание 4
Есть конфигурация nginx:  
java```
location / {
    add_header Content-Type text/plain;
    return 200 'Hello from k8s';
}
```

Выполните действия:  

Напишите yaml-файлы для развёртки nginx, в которых будут присутствовать:  

ConfigMap с конфигом nginx;  
Deployment, который бы подключал этот configmap;  
Ingress, который будет направлять запросы по префиксу /test на наш сервис.  