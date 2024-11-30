# Prometheus

Мониторинг — это процесс отслеживания и анализа ключевых показателей эффективности с целью выявления и решения проблем в режиме реального времени. Он включает в себя сбор данных, их обработку и визуализацию. Мониторинг может быть использован для оценки производительности системы, выявления потенциальных проблем и оптимизации процессов.

#### <a name='toc'>Содержание</a>
1. [Архитектура Prometheus](#1)
2. [Установка и запуск Node Exporter](#2)
3. [Запусткаем Prometheus и Grafana в Docker](#3)
4. [Результат настройки мониторинга](#4)
5. [Дополнительные источники](#5)

![Prometheus-Alertmanager-Grafana](https://github.com/user-attachments/assets/e837662f-19db-4689-8c83-0ab71de5dbf1)

## [[⬆]](#toc) <a name='1'>Архитектура Prometheus</a>

Prometheus является одним из самых популярных инструментов для мониторинга и предупреждения сбоев в работе различных систем. Он имеет веб-интерфейс и может быть настроен с помощью YAML-файлов. В данной статье мы познакомимся с основными компонентами Prometheus и рассмотрим процесс установки и настройки этой системы.

**Prometheus** — центральное звено, где хранятся все собранные метрики и имеется веб-интерфейс для их просмотра. Она собирает данные из различных источников, обрабатывает их и сохраняет на сервере. Prometheus также имеет веб-интерфейс, который позволяет пользователям просматривать собранные данные в реальном времени.

**Node Exporter** — агенты, собирающие метрики с целевых машин и передающие их на сервер Prometheus. Они могут быть настроены для сбора различных типов данных, таких как загрузка процессора, использование памяти и другие метрики производительности. Экспортеры устанавливаются на целевые машины и отправляют данные на сервер Prometheus через HTTP.

**AlertManager** —  уведомляет о возникших проблемах, обрабатывает оповещения и отправляет их на определенные адреса. Он поддерживает различные способы отправки уведомлений, такие как электронная почта, Slack, HipChat и PagerDuty. Также возможно настроить отправку оповещений через Telegram и другие сервисы.


## [[⬆]](#toc) <a name='2'>Установка и запуск Node Exporter</a>

_Скопируйте URL-адрес .tar.gz и загрузите его куда-нибудь на свой сервер с помощью wget или cURL:_

```
sudo wget https://github.com/prometheus/node_exporter/releases/download/v1.8.2/node_exporter-1.8.2.linux-amd64.tar.gz
```

_После загрузки последней версии Node Exporter приступайте к извлечению содержимого загруженного tar-файла с помощью следующей команды:_

```
tar xvfz node_exporter-*.*-amd64.tar.gz
```

_Содержимое архива будет извлечено в текущий каталог, извлеченный каталог будет содержать 3 файла:_

* LICENSE (license text file)
* node_exporter (binary)
* NOTICE (license text file)

_Вам нужно только переместить двоичный файл node_exporter в каталог /usr/local/bin вашей системы. Перейдите в каталог node_exporter:_

```
cd node_exporter-*.*-amd64
```

_А затем скопируйте двоичный файл с помощью следующей команды:_

```
sudo cp node_exporter /usr/local/bin
```

_Создание пользователя и владельца файла в системе для Node Exporter:_

```
sudo useradd --no-create-home --shell /bin/false node_exporter
sudo chown node_exporter:node_exporter /usr/local/bin/node_exporter
```

_Служба Node Exporter должна всегда запускаться при загрузке сервера, чтобы она всегда была доступна для получения информации. Создайте файл node_exporter.service с помощью nano:_

```
[Unit]
Description=Node Exporter
Wants=network-online.target
After=network-online.target

[Service]
User=node_exporter
Group=node_exporter
Type=simple
ExecStart=/usr/local/bin/node_exporter
Restart=always
RestartSec=3

[Install]
WantedBy=multi-user.target
```

![image](https://github.com/user-attachments/assets/1b94d578-a6ef-4d19-9a58-c6f06d7552dc)


_Перезагружаем демон, включаем службу node_exporter и проверяем статус:_

```
sudo systemctl daemon-reload
sudo systemctl start node_exporter
sudo systemctl status node_exporter
```
![image](https://github.com/user-attachments/assets/1c384a58-6f86-49f2-ade3-0200d6bc6fec)


_Тестирование службы Node Exporter_

```
curl http://localhost:9100/metrics
```

![image](https://github.com/user-attachments/assets/d433ee4d-88fa-4f75-935b-316550eb0128)


## [[⬆]](#toc) <a name='3'>Запускаем Prometheus и Grafana в Docker</a>

```
sudo docker run -d --name=prometheus -p 9090:9090 bitnami/prometheus:latest
sudo docker run -d --name=grafana -p 3000:3000 grafana/grafana-enterprise
```

![image](https://github.com/user-attachments/assets/c776ba22-3c68-4891-ba55-c3e51e01c58e)
![image](https://github.com/user-attachments/assets/ca16facc-1c24-4ddf-8758-b95bb0da81b3)
![image](https://github.com/user-attachments/assets/818fcd56-7d61-4719-836d-74c15d8324ad)

_Проверяем работу Prometheus_

![image](https://github.com/user-attachments/assets/b207d214-c6d8-4291-8964-6ee08d8e7c8c)

_Заходим под рутом в контейнер Prometheus_

```
docker exec -it -u 0 46a74c28a860 bash
```
![image](https://github.com/user-attachments/assets/9fb0e47d-26a0-47e4-b325-15e0bc26a59f)

_Редактируем конфигурационный файл Prometheus `nano ./conf/prometheus.yml` и добавляем наш хост_

```
- job_name: "localhost"
    static_configs:
      - targets: ["192.168.50.133:9100"]
```

![image](https://github.com/user-attachments/assets/8af05108-cec0-4c94-b66c-1a40f0a3ecc4)

_Рестартуем контейнер Prometheus_

```
docker restart 46a74c28a860
```

![image](https://github.com/user-attachments/assets/5b9a9e4e-cbdf-46cb-b27f-f3427e2bfc99)


_Проверяем работу Grafana_

![image](https://github.com/user-attachments/assets/96879ae8-28fc-427b-aa81-732d9335112f)

_Теперь мы переходим на вкладку “Data Sources” и добавляем наш Prometheus_

![image](https://github.com/user-attachments/assets/1efe7657-dc19-483e-bc7c-d08681b7fe62)


## [[⬆]](#toc) <a name='4'>Результат настройки мониторинга</a>

![image](https://github.com/user-attachments/assets/1823b9f7-a3df-4c76-b720-a237370f29c1)


## [[⬆]](#toc) <a name='5'>Дополнительные источники</a>

1. [Настраиваем мониторинг с Prometheus](https://pochemuchka.su/monitoring-with-prometheus-alertmanager-grafana/)
2. [Установка и настройка Prometheus](https://timeweb.cloud/tutorials/servers/ustanovka-i-nastrojka-prometheus)
3. [Система мониторинга Prometheus](https://help.reg.ru/support/servery-vps/oblachnyye-servery/ustanovka-programmnogo-obespecheniya/sistema-monitoringa-prometheus)
4. [Grafana и Prometheus](https://1cloud.ru/blog/grafana_prometheus_system)
5. [Установка и настройка Prometheus](https://losst.pro/ustanovka-i-nastrojka-prometheus)



























```
/usr/local/bin/prometheus
/usr/local/bin/promtool
/var/lib/prometheus
/etc/prometheus
```
