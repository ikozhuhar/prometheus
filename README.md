# prometheus

## Установка и запуск Node Exporter

_Скопируйте URL-адрес .tar.gz и загрузите его куда-нибудь на свой сервер с помощью wget или cURL:_

```
wget https://github.com/prometheus/node_exporter/releases/download/v<VERSION>/node_exporter-<VERSION>.<OS>-<ARCH>.tar.gz
```

После загрузки последней версии Node Exporter приступайте к извлечению содержимого загруженного tar-файла с помощью следующей команды:
```
tar xvfz node_exporter-*.*-amd64.tar.gz
```

_Содержимое zip-архива будет извлечено в текущий каталог, извлеченный каталог будет содержать 3 файла:_

* LICENSE (license text file)
* node_exporter (binary)
* NOTICE (license text file)

_Вам нужно только переместить двоичный файл node_exporter в каталог /usr/local/bin вашей системы. Перейдите в каталог node_exporter:_
```
cd node_exporter-*.*-amd64
``



cp node_exporter /usr/local/bin
useradd --no-create-home --shell /bin/false node_exporter
chown node_exporter:node_exporter /usr/local/bin/node_exporter
nano /etc/systemd/system/node_exporter.service
```

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

sudo systemctl daemon-reload

sudo systemctl start node_exporter

curl http://localhost:9100/metrics


























```
/usr/local/bin/prometheus
/usr/local/bin/promtool
/var/lib/prometheus
/etc/prometheus
```
