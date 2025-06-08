# Installation de l'alert manager sur Ubuntu

```
wget https://github.com/prometheus/alertmanager/releases/download/v0.27.0/alertmanager-0.27.0.linux-amd64.tar.gz 

tar -xvfz alertmanager-0.27.0.linux-amd64.tar.gz 

cd alertmanager-0.27.0.linux-amd64 

./alertmanager --config.file=alertmanager.yml & // Run AlertManager in background
```


# Configure Alert Manager as service systemd
### Create alert-manager.service file
```
[Unit]
Description=Prometheus Alert Manager
After=network.target
Wants=network.target

[Service]
Type=simple
User=prometheus
Group=prometheus
Restart=always
RestartSec=6s
WorkingDirectory=/etc/alertmanager/
ExecStart=/usr/local/bin/alertmanager --config.file=/etc/alertmanager/alertmanager.yml

[Install]
WantedBy=multi-user.target
```
## Start service 
```
systemctl enable --now alert-manager.service
systemctl status alert-manager.service
```