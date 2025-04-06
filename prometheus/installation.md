# Installation in ubuntu
```
sudo apt update

wget https://github.com/prometheus/prometheus/releases/download/v3.2.1/prometheus-3.2.1.linux-amd64.tar.gz

tar -xvf prometheus-3.2.1.linux-amd64.tar.gz

ls -la prometheus-3.2.1.linux-amd64
```

# Configure Prometheus with Systemd
```
mkdir -p /etc/prometheus /var/lib/prometheus

find / -type d -iname prometheus //Check if directory is created

cd prometheus-3.2.1.linux-amd64

mv prometheus promtool /usr/local/bin //Move prometheus and promtool to /usr/local/bin
ls /usr/local/bin/*

mv consoles console_libraries prometheus.yml /etc/prometheus/
ls /etc/prometheus

useradd -rs /bin/false prometheus
grep prometheus /etc/passwd /etc/group
```
# create file prometheus.service
vim prometheus.service

```
[Unit]
Description=Prometheus
Wants=network-online.target
After=network-online.target

[Service]
User=prometheus
Group=prometheus
Type=simple
restart=on-failure

ExecStart=/usr/local/bin/prometheus \
        --config.file /etc/prometheus/prometheus.yml \
        --storage.tsdb.path /var/lib/prometheus \
        --web.console.templates=/etc/prometheus/consoles \
        --web.console.libraries=/etc/prometheus/console_libraries \
        --web.listen-address=0.0.0.0:9090
        --web.enable-lifecycle \
        --log.level=info

[Install]
WantedBy=multi-user.target
```
## Start service 
```
systemctl enable --now prometheus.service
systemctl status prometheus.service
```
### Show Ip of server
```
ip add show eth1 | awk '/inet/ {print $2}'
```

