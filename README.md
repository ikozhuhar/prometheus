# prometheus

## Установка и запуск Node Exporter

_Скопируйте URL-адрес .tar.gz и загрузите его куда-нибудь на свой сервер с помощью wget или cURL:_

```
sudo wget https://github.com/prometheus/node_exporter/releases/download/v<VERSION>/node_exporter-<VERSION>.<OS>-<ARCH>.tar.gz
```

После загрузки последней версии Node Exporter приступайте к извлечению содержимого загруженного tar-файла с помощью следующей команды:
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

chown node_exporter:node_exporter /usr/local/bin/node_exporter
nano /etc/systemd/system/node_exporter.service
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



























```
/usr/local/bin/prometheus
/usr/local/bin/promtool
/var/lib/prometheus
/etc/prometheus
```
