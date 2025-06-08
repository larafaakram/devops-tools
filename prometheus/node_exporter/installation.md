# installation node exporter in Ubuntu

```
 wget https://github.com/prometheus/node_exporter/releases/download/v1.9.0/node_exporter-1.9.0.linux-amd64.tar.gz
 tar -xvf node_exporter-1.9.0.linux-amd64.tar.gz
 cd node_exporter-1.9.0.linux-amd64/
 ./node_exporter &
```


# Configure Node Exporter as service systemd
### Create node-exporter.service file
```
[Unit]
Description=Prometheus Node Exporter
After=network.target
Wants=network.target

[Service]
Type=simple
User=prometheus
Group=prometheus
Restart=always
RestartSec=6s
ExecStart=/usr/local/bin/node_exporter

[Install]
WantedBy=multi-user.target
```
## Start service 
```
systemctl enable --now node-exporter.service
systemctl status node-exporter.service
```
### Show Ip of server
```
ip add show eth1 | awk '/inet/ {print $2}'
```