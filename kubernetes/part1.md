# Домашнее задание к занятию «Kubernetes. Часть 1»

# Задание 1
Выполните действия:  

Запустите Kubernetes локально, используя k3s или minikube на свой выбор.  
Добейтесь стабильной работы всех системных контейнеров.  

[шпаргалка команд](https://kubernetes.io/ru/docs/reference/kubectl/cheatsheet/)

[install docker](https://github.com/Kovrei/home_work/blob/main/docker/docker%20install.md)
```
### install kubectl https://kubernetes.io/ru/docs/tasks/tools/install-kubectl/###

curl -LO https://dl.k8s.io/release/`curl -LS https://dl.k8s.io/release/stable.txt`/bin/linux/amd64/kubectl
chmod +x ./kubectl
sudo mv ./kubectl /usr/local/bin/kubectl
kubectl version --client

### ИЛИ install kubectl https://kubernetes.io/ru/docs/tasks/tools/install-kubectl/###

sudo apt-get update && sudo apt-get install -y apt-transport-https
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
echo "deb https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee -a /etc/apt/sources.list.d/kubernetes.list
sudo apt-get update
sudo apt-get install -y kubectl

### install minicube  https://minikube.sigs.k8s.io/docs/start/ ###
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
nano redis.yml
```
```
kubectl apply -f redis.yml

kubectl get po
kubectl get po -w

kubectl get nodes
kubectl get deploy
kubectl get rs
kubectl describe pod <pod-name>
kubectl get svc

#kubectl expose deploy/redis --port 6379

kubectl exec -it <pod-name> -- redis-cli -a 'password123'

127.0.0.1:6379> ping
PONG
127.0.0.1:6379> 

```
```
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
        image: redis:6.0.13
        env:
         #- name: REDIS_PASSWORD
         #  value: password123
         - name: MASTER
           value: "true"
        ports:
        - containerPort: 6379
```
```
kubectl apply -f redis.yml

kubectl get po
kubectl get po -w

kubectl exec -it <pod-name> -- redis-cli

127.0.0.1:6379> ping
PONG
127.0.0.1:6379> 

kubectl logs <pod-name> -f
```


# Задание 3 
Выполните действия:  

Напишите команды kubectl для контейнера из предыдущего задания:  
выполнения команды ps aux внутри контейнера;
```
kubectl exec --stdin --tty <pod-name> -- /bin/bash
#kubectl exec -it <pod-name> -- /bin/bash
```
```
#command shell
apt-get install -y procps
ps aux
ps aux | grep redis
```
просмотра логов контейнера за последние 5 минут;  
```
kubectl logs --since=5m redis-d96568758-4wdl5
```
[удаления контейнера](https://kubernetes.io/docs/reference/kubectl/generated/kubectl_delete/);  
```
kubectl delete deployment redis
```
проброса порта локальной машины в контейнер для отладки.  

```
kubectl port-forward  redis-76554fd456-pxbb5  7963:6379
```

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
~~~
nano cm.yml
~~~
```
apiVersion: v1
kind: ConfigMap
metadata:
  name: nginx-cm
data:
  default.conf: |
        server {
            listen       80 default_server;
            server_name _;
            location / {
              add_header Content-Type text/plain;
              return 200 'Hello from k8s';
            }
        }
```
Deployment, который бы подключал этот configmap;  
```
nano nginx.yml
```
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  selector:
    matchLabels:
      app: nginx
  replicas: 1
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx
        ports:
        - containerPort: 80
        volumeMounts:
            - name: nginx-cm
              mountPath: /etc/nginx/conf.d/
      volumes:
      - name: nginx-cm
        configMap:
          name: nginx-cm
```
```
nano svc.yml
```
```
apiVersion: v1
kind: Service
metadata:
  name: nginx-svc
spec:
  ports:
    - name: nginx-tcp
      protocol: TCP
      port: 80
      targetPort: 80
  selector:
    app: nginx-deployment
```
Ingress, который будет направлять запросы по префиксу /test на наш сервис.  
```
nano ingress.yml
```
```
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: nginx-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
  - host: my-test.ups
    http: 
      paths:
      - path: /test
        pathType: Prefix
        backend:
          service:
            name: nginx-svc
            port:
              number: 80
```
```
kubectl apply -f 

kubectl get po
kubectl get rs

kubectl get svc
kubectl expose deploy/nginx-deployment --port 80
kubectl get svc
kubectl get ep
kubectl get po -o wide
kubectl port-forward nginx-deployment-77d8468669-2mps2 8080:80 &
http:/localhost:8080
```

```
kubectl run --rm -it ngninx --image=curlimages/curl -- sh
###или
kubectl exec -it nginx-deployment-77d8468669-czhfv -- bash
curl nginx-deployment
curl nginx-deployment -I

kubectl logs nginx-deployment-77d8468669-czhfv
```
```
minikube addons list
minikube addons enable ingress
```
Error from server (InternalError): error when creating "nginx.yml": Internal error occurred: failed calling webhook "validate.nginx.ingress.kubernetes.io": failed to call webhook: Post "https://ingress-nginx-controller-admission.ingress-nginx.svc:443/networking/v1/ingresses?timeout=10s": tls: failed to verify certificate: x509: certificate signed by unknown authority
```
kubectl delete -A validatingWebhookConfiguration ingress-nginx-admission
```
```
curl my-test.ups
```


