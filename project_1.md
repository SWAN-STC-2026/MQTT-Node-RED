# Multi-Sensor Cloud IoT Dashboard using MQTT, Node-RED, AWS EC2, and Python

## Overview

This project demonstrates a real-time cloud IoT monitoring dashboard using:

* Python MQTT Publisher
* AWS EC2
* Mosquitto MQTT Broker
* Node-RED
* Node-RED Dashboard

The Python script simulates multiple sensor values:

* Temperature
* Humidity
* Pressure

The data is published using MQTT to an AWS EC2 server. Node-RED subscribes to the MQTT topic and displays:

* Live gauges
* Live text values
* Live charts

---

# System Architecture

```text
Python Multi-Sensor Publisher
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

# Features

* Real-time MQTT communication
* Multi-sensor JSON data publishing
* Live dashboard updates
* Temperature gauge
* Humidity gauge
* Pressure text display
* Temperature history chart
* Humidity history chart
* Cloud-hosted Node-RED dashboard

---

# Technologies Used

* AWS EC2 (Ubuntu)
* Mosquitto MQTT Broker
* Node-RED
* Node-RED Dashboard
* Python
* MQTT Protocol
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

## Configure Security Group

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

## Enable Mosquitto

```bash
sudo systemctl enable mosquitto
sudo systemctl start mosquitto
```

---

## Configure External MQTT Access

Open configuration:

```bash
sudo nano /etc/mosquitto/mosquitto.conf
```

Add:

```conf
listener 1883
allow_anonymous true
```

Restart Mosquitto:

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

---

# Open Node-RED

Open browser:

```text
http://YOUR_EC2_PUBLIC_IP:1880
```

---

# Install Dashboard Nodes

Inside Node-RED:

1. Open Menu (☰)
2. Manage Palette
3. Install
4. Search:

```text
node-red-dashboard
```

5. Install it

---

# MQTT Topic

This project uses:

```text
sensor/multi
```

---

# Python Multi-Sensor Publisher

Create:

```text
multi_sensor.py
```

Paste:

```python
import paho.mqtt.client as mqtt
import random
import time
import json

BROKER = "YOUR_EC2_PUBLIC_IP"
PORT = 1883
TOPIC = "sensor/multi"

client = mqtt.Client()

print("Connecting...")
client.connect(BROKER, PORT, 60)

while True:

    data = {
        "temperature": random.randint(20, 40),
        "humidity": random.randint(40, 90),
        "pressure": random.randint(990, 1020)
    }

    payload = json.dumps(data)

    client.publish(TOPIC, payload)

    print("Sent:", payload)

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

# Install Python MQTT Library

Open PowerShell:

```powershell
python -m pip install paho-mqtt
```

---

# Run Python Publisher

```powershell
python multi_sensor.py
```

Expected Output:

```text
Sent: {"temperature": 31, "humidity": 72, "pressure": 1008}
```

---

# Create Node-RED Flow

## Flow Structure

```text
mqtt in → json ──→ temperature gauge
                 ├→ humidity gauge
                 ├→ pressure text
                 ├→ temp function → temp chart
                 ├→ humidity function → humidity chart
                 └→ debug
```

---

# IMPORTANT JSON NODE CONFIGURATION

This is the most important part of the project.

The MQTT payload arrives as a STRING.

To access:

```text
msg.payload.temperature
```

Node-RED must convert the payload into a JavaScript Object.

---

## JSON Node Configuration

Double-click JSON node.

Set:

| Field  | Value                               |
| ------ | ----------------------------------- |
| Action | Always convert to JavaScript Object |

---

## Debug Verification

Correct debug output should show:

```text
msg.payload : Object
```

NOT:

```text
msg.payload : string
```

---

# Temperature Gauge Configuration

| Field        | Value                       |
| ------------ | --------------------------- |
| Label        | Temperature                 |
| Value Format | {{msg.payload.temperature}} |
| Units        | °C                          |

---

# Humidity Gauge Configuration

| Field        | Value                    |
| ------------ | ------------------------ |
| Label        | Humidity                 |
| Value Format | {{msg.payload.humidity}} |
| Units        | %                        |

---

# Pressure Text Configuration

| Field        | Value                        |
| ------------ | ---------------------------- |
| Label        | Pressure                     |
| Value Format | {{msg.payload.pressure}} hPa |

---

# Dashboard Group Configuration

| Field | Value              |
| ----- | ------------------ |
| Tab   | IoT Monitoring     |
| Group | Environmental Data |

---

# Temperature Chart

## Function Node

```javascript
msg.payload = Number(msg.payload.temperature);
return msg;
```

Connect:

```text
json → function → chart
```

---

# Humidity Chart

## Function Node

```javascript
msg.payload = Number(msg.payload.humidity);
return msg;
```

Connect:

```text
json → function → chart
```

---

# Deploy Flow

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

---

# Dashboard Features

The dashboard displays:

* Live temperature gauge
* Live humidity gauge
* Live pressure value
* Live temperature chart
* Live humidity chart

---

# Common Issue and Solution

## Problem

Dashboard widgets do not update even though MQTT messages appear in Debug.

---

## Cause

Payload is still a STRING.

Example incorrect debug:

```text
msg.payload : string[48]
```

---

## Correct Output

```text
msg.payload : Object
```

---

## Solution

Configure JSON node:

```text
Always convert to JavaScript Object
```

This enables:

```text
msg.payload.temperature
msg.payload.humidity
msg.payload.pressure
```

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

# Final Result

You successfully built a cloud-based real-time IoT monitoring system using:

* Python MQTT Publisher
* AWS EC2
* Mosquitto MQTT Broker
* Node-RED
* Live Dashboard Visualization

---

# Future Improvements

* Add ESP32/Arduino sensors
* Store data in database
* Add alert notifications
* Integrate InfluxDB + Grafana
* Add HTTPS security
* Add MQTT authentication
* Use PM2 for Node-RED auto-start
* Deploy production-grade IoT architecture

---

# License

This project is intended for educational and learning purposes.
