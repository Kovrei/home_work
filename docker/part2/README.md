# Домашнее задание к занятию «Docker. Часть 2»

# Задание 1
Напишите ответ в свободной форме, не больше одного абзаца текста.

Установите Docker Compose и опишите, для чего он нужен и как может улучшить вашу жизнь.

[docker install](https://github.com/Kovrei/home_work/blob/main/docker/docker%20install.md)

# Задание 2
Выполните действия и приложите текст конфига на этом этапе.

Создайте файл docker-compose.yml и внесите туда первичные настройки:

version;
services;
volumes;
networks.
При выполнении задания используйте подсеть 10.5.0.0/16. Ваша подсеть должна называться: <ваши фамилия и инициалы>-my-netology-hw. Все приложения из последующих заданий должны находиться в этой конфигурации.

nano docker-compose.yml

```
version: '3'

services:

volumes:

networks:
  monitoring-stack:
    name: oau-netology-hw
    driver: bridge
      ipam:
        config:
          - subnet: 10.5.0.0/16
            gateway: 10.5.0.1
```

# Задание 3
Выполните действия:

Создайте конфигурацию docker-compose для Prometheus с именем контейнера <ваши фамилия и инициалы>-netology-prometheus.
Добавьте необходимые тома с данными и конфигурацией (конфигурация лежит в репозитории в директории 6-04/prometheus).
Обеспечьте внешний доступ к порту 9090 c докер-сервера.

```
mkdir ...  
nano docker-compose.yml 
```
[docker-compose.yml](https://github.com/Kovrei/home_work/blob/main/docker/part2/prometheus/docker-compose.prometheus.yml)
```
mkdir -p ./{prometheus,grafana,pushgateway}  
nano prometheus/prometheus.yml
```
[prometheus.yml](https://github.com/Kovrei/home_work/blob/main/docker/part2/prometheus/prometheus.yml)
```
sudo ufw status  
sudo ufw enable  
sudo ufw status  
sudo ufw default deny incoming  
sudo ufw default allow outgoing  
sudo ufw allow 9090/tcp  
sudo ufw status  
```
```
sudo docker compose up -d  
sudo docker compose ps 
```

# Задание 4
Выполните действия:

Создайте конфигурацию docker-compose для Pushgateway с именем контейнера <ваши фамилия и инициалы>-netology-pushgateway.
Обеспечьте внешний доступ к порту 9091 c докер-сервера.
```
nano docker-compopse.pushgateway.yml
```
[docker-compose.pushgateway.yml](https://github.com/Kovrei/home_work/blob/main/docker/part2/pushgateway/docker-compose.prometheus-pushgateway.yml)
```
sudo docker compose down  
sudo docker compose  -f docker-compose.yml -f docker-compose.pushgateway.yml up -d  
sudo docker compose ps  
echo "docker 2" | curl --data-binary @- http://localhost:9091/metrics/job/netology  
```

# Задание 5
Выполните действия:

Создайте конфигурацию docker-compose для Grafana с именем контейнера <ваши фамилия и инициалы>-netology-grafana.
Добавьте необходимые тома с данными и конфигурацией (конфигурация лежит в репозитории в директории 6-04/grafana).
Добавьте переменную окружения с путем до файла с кастомными настройками (должен быть в томе), в самом файле пропишите логин=<ваши фамилия и инициалы> пароль=netology.
Обеспечьте внешний доступ к порту 3000 c порта 80 докер-сервера.



```
sudo docker pull grafana/grafana  
mkdir grafana  
nano grafana/custom.ini
nano docker-compose.grafana.yml
```
[custom.ini](https://github.com/Kovrei/home_work/blob/main/docker/part2/grafana/custom.ini)  
[docker-compose.grafana.yml](https://github.com/Kovrei/home_work/blob/main/docker/part2/grafana/docker-compose.grafana.yml) 
```
sudo docker compose -f docker-compose.yml -f docker-compose.pushgateway.yml -f docker-compose.grafana.yml up -d  
sudo docker compose ps  
```


# Задание 6
Выполните действия.

Настройте поочередность запуска контейнеров.
Настройте режимы перезапуска для контейнеров.
Настройте использование контейнерами одной сети.
Запустите сценарий в detached режиме.
```
sudo docker compose up -d
```
[doker-compose.yml](https://github.com/Kovrei/home_work/blob/main/docker/part2/docker-compose.yml)
```
sudo doker compose ps
echo "oau 5" | curl --data-binary @- http://158.160.49.40:9091/metrics/job/netology
```


# Задание 7
Выполните действия.

Выполните запрос в Pushgateway для помещения метрики <ваши фамилия и инициалы> со значением 5 в Prometheus: echo "<ваши фамилия и инициалы> 5" | curl --data-binary @- http://localhost:<внешний порт выбранный вами в задании 4>/metrics/job/netology.
Залогиньтесь в Grafana с помощью логина и пароля из предыдущего задания.
Cоздайте Data Source Prometheus (Home -> Connections -> Data sources -> Add data source -> Prometheus -> указать "Prometheus server URL = http://prometheus:9090" -> Save & Test).
Создайте график на основе добавленной в пункте 5 метрики (Build a dashboard -> Add visualization -> Prometheus -> Select metric -> Metric explorer -> <ваши фамилия и инициалы -> Apply.
В качестве решения приложите:

docker-compose.yml целиком;
скриншот команды docker ps после запуске docker-compose.yml;
скриншот графика, постоенного на основе вашей метрики.

```
sudo docker compose up -d
```
[doker-compose.yml](https://github.com/Kovrei/home_work/blob/main/docker/part2/docker-compose.yml)
```

# Задание 8
Выполните действия:

Остановите и удалите все контейнеры одной командой.
В качестве решения приложите скриншот консоли с проделанными действиями.

```
sudo docker stop $(sudo docker ps -a -q) && sudo docker rm $(sudo docker ps -a -q) && sudo docker rmi $(sudo docker ps -a -q)
```


# Дополнительные задания* (со звёздочкой)
Их выполнение необязательное и не влияет на получение зачёта по домашнему заданию. Можете их решить, если хотите лучше разобраться в материале.

# Задание 9*
Выполните действия:

Создайте конфигурацию docker-compose для Alertmanager с именем контейнера <ваши фамилия и инициалы>-netology-alertmanager.
Добавьте необходимые тома с данными и конфигурацией, сеть, режим и очередность запуска.
Обновите конфигурацию Prometheus (необходимые изменения ищите в презентации или документации) и перезапустите его.
Обеспечьте внешний доступ к порту 9093 c докер-сервера.
В качестве решения приложите скриншот с событием из Alertmanager.

```
sudo docker compose up -d
```
[doker-compose.yml](https://github.com/Kovrei/home_work/blob/main/docker/part2/alertmanager/docker-compose.alertmanager.yml)
```
sudo doker compose ps
```

# Задание 10*
Запустите свой сценарий на чистом железе без предзагруженных образов.

Ответьте на вопросы в свободной форме:

Опишите выполненный вами процесс развертывания сценария.
Как вы думаете зачем может понадобиться такой способ развертывания?

```
sudo docker compose up -d
```
[doker-compose.yml](https://github.com/Kovrei/home_work/blob/main/docker/part2/docker-compose.all.yml)
```
sudo doker compose ps
```

