# 📊 Installing Prometheus and Grafana on Ubuntu 24.04

## 📌 Table of Contents

- [📖 Introduction](#-introduction)
- [⚙️ Step 1: Updating System Packages](#️-step-1-updating-system-packages)
- [📡 Step 2: Installing Prometheus](#-step-2-installing-prometheus)
- [🖥️ Step 3: Installing Node Exporter](#️-step-3-installing-node-exporter)
- [📊 Step 4: Installing Grafana](#-step-4-installing-grafana)
- [✅ Conclusion](#-conclusion)

---

## 📖 Introduction

This guide provides a comprehensive, step-by-step approach to installing **Prometheus** and **Grafana** on Ubuntu 24.04. These tools are widely used for system monitoring and performance analysis.

- **🚀 Prometheus**: An open-source monitoring system designed to collect, process, and store time-series data metrics.
- **📊 Grafana**: A powerful visualization tool that enables users to create interactive monitoring dashboards and alerts.

By the end of this guide, you will have a fully functional monitoring stack with Prometheus, Grafana, and Node Exporter. 🎯

---

## ⚙️ Step 1: Updating System Packages

Before installing any software, ensure that your system packages and dependencies are updated.

```bash
# Update package list and upgrade installed packages
sudo apt update && sudo apt upgrade -y
```

---

## 📡 Step 2: Installing Prometheus

Download and install the latest version of Prometheus for robust monitoring capabilities.

```bash
# Create a dedicated Prometheus user
sudo useradd --no-create-home --shell /bin/false prometheus

# Create necessary directories
sudo mkdir /etc/prometheus /var/lib/prometheus

# Change ownership of directories
sudo chown prometheus:prometheus /etc/prometheus /var/lib/prometheus

# Download Prometheus
cd /tmp
wget https://github.com/prometheus/prometheus/releases/download/v2.46.0/prometheus-2.46.0.linux-amd64.tar.gz

# Extract and move files
tar -xvf prometheus-2.46.0.linux-amd64.tar.gz
cd prometheus-2.46.0.linux-amd64
sudo mv console* /etc/prometheus
sudo mv prometheus.yml /etc/prometheus

# Set appropriate permissions
sudo chown -R prometheus:prometheus /etc/prometheus
```

### 🛠️ Configuring Prometheus

```bash
# Move configuration files
sudo mv prometheus /usr/local/bin/

# Change ownership of configuration files
sudo chown prometheus:prometheus /usr/local/bin/prometheus
#Prometheus configuration file
sudo nano /etc/prometheus/prometheus.yml
```

### 🔧 Creating a Systemd Service File

```bash
sudo tee /etc/systemd/system/prometheus.service <<EOF
[Unit]
Description=Prometheus Monitoring Service
Wants=network-online.target
After=network-online.target

[Service]
User=prometheus
Group=prometheus
Type=simple
ExecStart=/usr/local/bin/prometheus --config.file=/etc/prometheus/prometheus.yml --storage.tsdb.path=/var/lib/prometheus --web.console.templates=/etc/prometheus/consoles --web.console.libraries=/etc/prometheus/console_libraries

[Install]
WantedBy=multi-user.target
EOF
```

### 🚀 Starting and Enabling Prometheus

```bash
sudo systemctl daemon-reload
sudo systemctl start prometheus
sudo systemctl enable prometheus
```

### ✅ Verifying the Installation

```bash
sudo systemctl status prometheus
```

---

## 🖥️ Step 3: Installing Node Exporter

Node Exporter is used to collect hardware and operating system metrics.

```bash

# Download Node Exporter
cd /tmp
wget https://github.com/prometheus/node_exporter/releases/download/v1.6.1/node_exporter-1.6.1.linux-amd64.tar.gz

# Extract and move files
sudo tar xvf node_exporter-*-linux-amd64.tar.gz
sudo mv node_exporter-*.*-amd64/node_exporter /usr/local/bin/

#Create a node_exporter user to run the node exporter service
sudo useradd -rs /bin/false node_exporter
```

### 🔧 Creating a Systemd Service File

```bash
sudo tee /etc/systemd/system/node_exporter.service <<EOF
[Unit]
Description=Node Exporter Service
Wants=network-online.target
After=network-online.target

[Service]
User=node_exporter
Group=node_exporter
Type=simple
ExecStart=/usr/local/bin/node_exporter

[Install]
WantedBy=multi-user.target
EOF
```

### 🚀 Starting and Enabling Node Exporter

```bash
sudo systemctl daemon-reload
sudo systemctl start node_exporter
sudo systemctl enable node_exporter
```

### ✅ Verifying the Installation

```bash
sudo systemctl status node_exporter
```

###Configure the Node Exporter as a Prometheus target

```bash
sudo nano /etc/prometheus/prometheus.yml
```

```bash
- job_name: 'Node_Exporter'

    scrape_interval: 5s

    static_configs:

      - targets: ['<Server_IP_of_Node_Exporter_Machine>:9100']
```
Now restart the Prometheus Service
```bash
sudo systemctl restart prometheus
```
---

## 📊 Step 4: Installing Grafana

Grafana is used for visualizing and analyzing system metrics collected by Prometheus.

```bash
# Add the Grafana repository
sudo apt install -y software-properties-common
wget -q -O - https://packages.grafana.com/gpg.key | sudo apt-key add -
sudo add-apt-repository "deb https://packages.grafana.com/oss/deb stable main"

# Install Grafana
sudo apt update
sudo apt install -y grafana
```

### 🚀 Starting and Enabling Grafana

```bash
sudo systemctl start grafana-server
sudo systemctl enable grafana-server
```

### ✅ Verifying the Installation

```bash
sudo systemctl status grafana-server
```

---

### 🌐 **Accessing Grafana**  
Open a web browser and navigate to `http://your_ip:3000/` to access the **Grafana** dashboard.

---

### 🌐 **Accessing Prometheus**  
Open a web browser and navigate to `http://server-IP-or-Hostname:9090` to access the **Prometheus** dashboard.

---

![Screenshot](https://github.com/PHIOPHI/LinuxMonitorStack/blob/5b81c19881c79b9652367cf7d59e2fdf3019d426/Screenshot%20from%202025-02-25%2009-47-45.png)

---

## ✅ **Conclusion**  

By following this guide, you have successfully installed and configured **Prometheus**, **Node Exporter**, and **Grafana** on **Ubuntu 24.04**. 🎯 

- **📡 Prometheus** is now collecting system metrics efficiently. 
- **🖥️ Node Exporter** is actively monitoring hardware and OS data. 
- **📊 Grafana** is set up for data visualization and analysis. 

With these tools, you can create customized dashboards, set alerts, and gain deep insights into your system's performance. 🚀 

**Happy monitoring!** 🎉

---

