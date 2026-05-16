# MQTT-Node-RED

# Simple Cloud MQTT + Node-RED Live Dashboard

## Overview

This project demonstrates a very simple cloud IoT pipeline using:

* Python MQTT Publisher (Local Machine)
* Mosquitto MQTT Broker (AWS EC2)
* Node-RED Dashboard

A Python script generates a random value every 2 seconds and publishes it using MQTT to a public AWS EC2 server. Node-RED subscribes to the MQTT topic and displays the live value on a dashboard.

---

# System Architecture

```text
Python Publisher (Local PC)
        ↓ MQTT
AWS EC2 Public IP
        ↓
Mosquitto MQTT Broker
        ↓
Node-RED
        ↓
Dashboard UI
```

---

# Technologies Used

* AWS EC2 (Ubuntu)
* Mosquitto MQTT Broker
* Node-RED
* Python
* MQTT Protocol
* node-red-dashboard
* paho-mqtt

---

# AWS EC2 Setup

## Launch EC2 Instance

Recommended:

| Setting       | Value               |
| ------------- | ------------------- |
| OS            | Ubuntu Server       |
| Instance Type | t2.micro / t3.micro |

---

## Security Group Rules

Allow inbound ports:

| Type       | Port |
| ---------- | ---- |
| SSH        | 22   |
| Custom TCP | 1880 |
| Custom TCP | 1883 |

---

# Install Mosquitto MQTT Broker

## Update Ubuntu

```bash
sudo apt update && sudo apt upgrade -y
```

---

## Install Mosquitto

```bash
sudo apt install mosquitto mosquitto-clients -y
```

---

## Enable and Start Mosquitto

```bash
sudo systemctl enable mosquitto
sudo systemctl start mosquitto
```

---

## Configure External Access

Open configuration:

```bash
sudo nano /etc/mosquitto/mosquitto.conf
```

Add at bottom:

```conf
listener 1883
allow_anonymous true
```

Save and restart:

```bash
sudo systemctl restart mosquitto
```

---

## Verify MQTT Port

```bash
sudo ss -tulnp | grep 1883
```

Expected:

```text
0.0.0.0:1883
```

---

# Install Node-RED

## Install Node.js and npm

```bash
sudo apt install nodejs npm -y
```

---

## Install Node-RED

```bash
sudo npm install -g --unsafe-perm node-red
```

---

## Start Node-RED

```bash
node-red
```

Expected:

```text
Server now running at http://127.0.0.1:1880/
```

---

# Open Node-RED

Open browser:

```text
http://YOUR_EC2_PUBLIC_IP:1880
```

Example:

```text
http://3.106.xxx.xxx:1880
```

---

# Install Dashboard Nodes

Inside Node-RED:

1. Open Menu (☰)
2. Select:

```text
Manage Palette
```

3. Open:

```text
Install
```

4. Search:

```text
node-red-dashboard
```

5. Install it.

---

# Create Node-RED Flow

## Flow Structure

```text
mqtt in → text
        ↘
         debug
```

---

## MQTT Input Node Configuration

### Broker Configuration

| Field  | Value     |
| ------ | --------- |
| Server | localhost |
| Port   | 1883      |

---

### Topic Configuration

| Field | Value      |
| ----- | ---------- |
| Topic | test/value |

---

## Dashboard Text Node Configuration

| Field        | Value           |
| ------------ | --------------- |
| Label        | Live Value      |
| Value Format | {{msg.payload}} |

---

## Dashboard Group Configuration

| Field | Value     |
| ----- | --------- |
| Tab   | MQTT Demo |
| Group | Live Data |

---

## Deploy Flow

Click:

```text
Deploy
```

---

# Open Dashboard

Open:

```text
http://YOUR_EC2_PUBLIC_IP:1880/ui
```

You should see:

```text
Live Value
```

---

# Install Python

Download Python:

[https://www.python.org/downloads/](https://www.python.org/downloads/)

IMPORTANT:

Enable:

```text
Add Python to PATH
```

during installation.

---

# Install MQTT Python Library

Open PowerShell:

```powershell
python -m pip install paho-mqtt
```

---

# Create Python Publisher

Create:

```text
publisher.py
```

Paste:

```python
import paho.mqtt.client as mqtt
import random
import time

BROKER = "YOUR_EC2_PUBLIC_IP"
PORT = 1883
TOPIC = "test/value"

client = mqtt.Client()

print("Connecting...")
client.connect(BROKER, PORT, 60)

while True:
    value = random.randint(1, 100)

    client.publish(TOPIC, str(value))

    print("Sent:", value)

    time.sleep(2)
```

---

## IMPORTANT

Replace:

```python
YOUR_EC2_PUBLIC_IP
```

with your actual EC2 public IP.

Example:

```python
BROKER = "3.106.xxx.xxx"
```

---

# Run Python Publisher

Open terminal inside project folder:

```powershell
python publisher.py
```

Expected:

```text
Connecting...
Sent: 45
Sent: 67
Sent: 22
```

---

# Live Dashboard

Open:

```text
http://YOUR_EC2_PUBLIC_IP:1880/ui
```

The dashboard will update every 2 seconds.

---

# Final Result

You successfully built:

* MQTT Publisher using Python
* Cloud MQTT Broker on AWS EC2
* Node-RED MQTT Subscriber
* Live Dashboard Visualization

---

# Useful Commands

## Check Mosquitto Status

```bash
sudo systemctl status mosquitto
```

---

## Restart Mosquitto

```bash
sudo systemctl restart mosquitto
```

---

## Start Node-RED

```bash
node-red
```

---

# Future Improvements

* Add live chart visualization
* Send JSON sensor data
* Integrate ESP32/Arduino
* Store data in database
* Add MQTT authentication
* Secure Node-RED with HTTPS
* Use PM2 for auto-start
* Deploy production-grade IoT architecture

---

# License

This project is for educational and learning purposes.
