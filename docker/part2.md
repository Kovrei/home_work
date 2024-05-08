# Домашнее задание к занятию «Docker. Часть 2»

# Задание 1
Напишите ответ в свободной форме, не больше одного абзаца текста.

Установите Docker Compose и опишите, для чего он нужен и как может улучшить вашу жизнь.

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
```

# Задание 2
Выполните действия и приложите текст конфига на этом этапе.

Создайте файл docker-compose.yml и внесите туда первичные настройки:

version;
services;
volumes;
networks.
При выполнении задания используйте подсеть 10.5.0.0/16. Ваша подсеть должна называться: <ваши фамилия и инициалы>-my-netology-hw. Все приложения из последующих заданий должны находиться в этой конфигурации.

```
nano docker-compose.yml
```
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
mkdir
nano docker-compose.yml
```
```
version: '3'

services:
  prometheus:
    image: prom/prometheus:v2.47.2
    container_name: oau-netology-prometheus
    command: --web.enable-lifecycle --config.file=/etc/prometheus/prometheus.yml
    ports:
      - 9090:9090
    volumes:
      - ./prometheus:/etc/prometheus
      - prometheus-data:/prometheus
    networks:
      - monitoring-stack
    restart: always

volumes:
  prometheus-data:

networks:
  monitoring-stack:
    name: oau-netology-hw
    driver: bridge
    ipam:
      config:
        - subnet: 10.5.0.0/16
          gateway: 10.5.0.1
```
```
mkdir -p ./{prometheus,grafana,pushgateway}
nano prometheus/prometheus.yml
```
```
# my global config
global:
  scrape_interval: 15s # Set the scrape interval to every 15 seconds. Default is every 1 minute.
  evaluation_interval: 15s # Evaluate rules every 15 seconds. The default is every 1 minute.
  # scrape_timeout is set to the global default (10s).

# Alertmanager configuration
alerting:
  alertmanagers:
    - static_configs:
        - targets:
            # - alertmanager:9093

# Load rules once and periodically evaluate them according to the global 'evaluation_interval'.
rule_files:
  # - "first_rules.yml"
  # - "second_rules.yml"

# A scrape configuration containing exactly one endpoint to scrape:
# Here it's Prometheus itself.
scrape_configs:
  # The job name is added as a label `job=<job_name>` to any timeseries scraped from this config.
  # - job_name: "docker-server"
    # metrics_path defaults to '/metrics'
    # scheme defaults to 'http'.
  #   static_configs:
  #     - targets: ["172.17.0.1:9100"]

  - job_name: 'pushgateway'
    honor_labels: true
    static_configs:
      - targets: ["pushgateway:9091"]
```
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
sudo docker compose ls
```

# Задание 4
Выполните действия:

Создайте конфигурацию docker-compose для Pushgateway с именем контейнера <ваши фамилия и инициалы>-netology-pushgateway.
Обеспечьте внешний доступ к порту 9091 c докер-сервера.

```
version: '3'

services:
  prometheus:
    image: prom/prometheus:v2.47.2
    container_name: oau-netology-prometheus
    command: --web.enable-lifecycle --config.file=/etc/prometheus/prometheus.yml
    ports:
      - 9090:9090
    volumes:
      - ./prometheus:/etc/prometheus
      - prometheus-data:/prometheus
    networks:
      - monitoring-stack
    restart: always
    
  pushgateway:
    image: prom/pushgateway:v1.6.2
    container_name: oau-netology-pushgateway
    ports:
      - 9091:9091
    networks:
      - monitoring-stack
    depends_on:
      - prometheus
    restart: unless-stopped

volumes:
  prometheus-data:

networks:
  monitoring-stack:
    name: oau-netology-hw
    driver: bridge
    ipam:
      config:
        - subnet: 10.5.0.0/16
          gateway: 10.5.0.1
```
```
sudo ufw allow 9191/tcp
```
```
sudo docker compose down
sudo docker compose up
```

# Задание 5
Выполните действия:

Создайте конфигурацию docker-compose для Grafana с именем контейнера <ваши фамилия и инициалы>-netology-grafana.
Добавьте необходимые тома с данными и конфигурацией (конфигурация лежит в репозитории в директории 6-04/grafana).
Добавьте переменную окружения с путем до файла с кастомными настройками (должен быть в томе), в самом файле пропишите логин=<ваши фамилия и инициалы> пароль=netology.
Обеспечьте внешний доступ к порту 3000 c порта 80 докер-сервера.
# Задание 6
Выполните действия.

Настройте поочередность запуска контейнеров.
Настройте режимы перезапуска для контейнеров.
Настройте использование контейнерами одной сети.
Запустите сценарий в detached режиме.
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
Задание 8
Выполните действия:

Остановите и удалите все контейнеры одной командой.
В качестве решения приложите скриншот консоли с проделанными действиями.

# Дополнительные задания* (со звёздочкой)
Их выполнение необязательное и не влияет на получение зачёта по домашнему заданию. Можете их решить, если хотите лучше разобраться в материале.

# Задание 9*
Выполните действия:

Создайте конфигурацию docker-compose для Alertmanager с именем контейнера <ваши фамилия и инициалы>-netology-alertmanager.
Добавьте необходимые тома с данными и конфигурацией, сеть, режим и очередность запуска.
Обновите конфигурацию Prometheus (необходимые изменения ищите в презентации или документации) и перезапустите его.
Обеспечьте внешний доступ к порту 9093 c докер-сервера.
В качестве решения приложите скриншот с событием из Alertmanager.

# Задание 10*
Запустите свой сценарий на чистом железе без предзагруженных образов.

Ответьте на вопросы в свободной форме:

Опишите выполненный вами процесс развертывания сценария.
Как вы думаете зачем может понадобиться такой способ развертывания?
