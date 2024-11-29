# prometheus

## Установка и запуск Node Exporter

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
``

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


_Перезагрузите демон, включаем службу node_exporter и проверяем статус:_

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


## Запусткаем Prometheus и Grafana в Docker

```
sudo docker run -d --name=prometheus -p 9090:9090 bitnami/prometheus:latest
sudo docker run -d --name=grafana -p 3000:3000 grafana/grafana-enterprise
```

![image](https://github.com/user-attachments/assets/c776ba22-3c68-4891-ba55-c3e51e01c58e)
![image](https://github.com/user-attachments/assets/ca16facc-1c24-4ddf-8758-b95bb0da81b3)
![image](https://github.com/user-attachments/assets/818fcd56-7d61-4719-836d-74c15d8324ad)

_Проверка работы Grafana_
![image](https://github.com/user-attachments/assets/96879ae8-28fc-427b-aa81-732d9335112f)

_Заходим под рутом в контейнер прометеуса_

```
docker exec -it -u 0 46a74c28a860 bash
```
![image](https://github.com/user-attachments/assets/9fb0e47d-26a0-47e4-b325-15e0bc26a59f)

_Редактируем конфигурационный файл Прометеуса nano ./conf/prometheus.yml и добавляем наш хост_

```
- job_name: "localhost"
    static_configs:
      - targets: ["192.168.50.133:9100"]
```

![image](https://github.com/user-attachments/assets/8af05108-cec0-4c94-b66c-1a40f0a3ecc4)

_Рестартуем контейнер Прометеуса_

```
docker restart 46a74c28a860
```

![image](https://github.com/user-attachments/assets/5b9a9e4e-cbdf-46cb-b27f-f3427e2bfc99)

## Смтрим результат мониторинга нашего хоста

![image](https://github.com/user-attachments/assets/83075e0d-c698-4903-a2b3-cb964a8ff618)































```
/usr/local/bin/prometheus
/usr/local/bin/promtool
/var/lib/prometheus
/etc/prometheus
```
