# Install Blackbox in Ubuntu
```
 wget https://github.com/prometheus/blackbox_exporter/releases/download/v0.26.0/blackbox_exporter-0.26.0.linux-amd64.tar.gz

 tar -xvf blackbox_exporter-0.26.0.linux-amd64.tar.gz

 cd blackbox_exporter-0.26.0.linux-amd64

 ./blackbox_exporter &
```

# Configure Blackbox as service systemd
### Create blackbox.service file
```
[Unit]
Description=Prometheus blackbox Exporter
After=network.target
Wants=network.target

[Service]
Type=simple
User=prometheus
Group=prometheus
Restart=always
RestartSec=6s
ExecStart=/usr/local/bin/blackbox_exporter --config.file="/etc/blackbox/blackbox.yml"

[Install]
WantedBy=multi-user.target
```
## Start service 
```
systemctl enable --now blackbox.service
systemctl status blackbox.service
```
### Show Ip of server
```
ip add show eth1 | awk '/inet/ {print $2}'
```
