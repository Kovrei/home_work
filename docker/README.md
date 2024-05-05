# Домашнее задание к занятию «Docker. Часть 1» - Андрей Осипенков

## Задание 1
Установите Docker.  
Запустите образ hello-world.  
Удалите образ hello-world.  

https://docs.docker.com/engine/install/ubuntu/

### Add Docker's official GPG key:
```java
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc
```

### Add the repository to Apt sources:
```
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
```
```
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
sudo docker run hello-world
```

## Задание 2
Найдите в Docker Hub образ Apache и запустите его на 80 порту вашей ВМ.
Откройте страницу http://localhost и убедитесь, что видите приветвенную страницу Apache.

## Задание 3
Создайте свой Docker образ с Apache и подмените стандартную страницу index.html на страницу, содержащую ваши ФИО.
Запустите ваш образ, откройте страницу http://localhost и убедитесь, что страница изменилась.




