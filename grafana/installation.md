# Installation of grafana in Ubuntu

##
```
# Install dependencies:
 sudo apt-get install -y adduser libfontconfig1 musl

# Download and install Grafana:
 wget https://dl.grafana.com/enterprise/release/grafana-enterprise_11.5.2_amd64.deb
 sudo dpkg -i grafana-enterprise_11.5.2_amd64.deb

# Start grafana
 sudo systemctl start grafana-server
```

