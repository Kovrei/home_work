# Домашнее задание к занятию «Kubernetes. Часть 1»

# Задание 1
Выполните действия:  

Запустите Kubernetes локально, используя k3s или minikube на свой выбор.  
Добейтесь стабильной работы всех системных контейнеров.  

[install docker](https://github.com/Kovrei/home_work/blob/main/docker/docker%20install.md)
```
#
#1########### install kubectl https://kubernetes.io/ru/docs/tasks/tools/install-kubectl/
#
curl -LO https://dl.k8s.io/release/`curl -LS https://dl.k8s.io/release/stable.txt`/bin/linux/amd64/kubectl
chmod +x ./kubectl
sudo mv ./kubectl /usr/local/bin/kubectl
kubectl version --client
#
### or install kubectl https://kubernetes.io/ru/docs/tasks/tools/install-kubectl/
#
sudo apt-get update && sudo apt-get install -y apt-transport-https
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
echo "deb https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee -a /etc/apt/sources.list.d/kubernetes.list
sudo apt-get update
sudo apt-get install -y kubectl
#
#2########## install minicube  https://minikube.sigs.k8s.io/docs/start/
#
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube && rm minikube-linux-amd64

minikube start

kubectl get po -A
minikube kubectl -- get po -A
```



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
```
kubectl apply -f redis.yml
kubectl get po
kubectl describe pod <pod-name>
kubectl get nod
kubectl get deploy
kubectl get rs

kubectl expose deploy/redis --port 6379
kubectl get svc
kubectl run --rm -it redis --image=curlimages/curl -- sh
curl redis
curl redis -I

kubectl logs <pod-name> -f

```


# Задание 3 
Выполните действия:  

Напишите команды kubectl для контейнера из предыдущего задания:  
выполнения команды ps aux внутри контейнера;  
просмотра логов контейнера за последние 5 минут;  
удаления контейнера;  
проброса порта локальной машины в контейнер для отладки.  
# Задание 4
Есть конфигурация nginx:

```
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
