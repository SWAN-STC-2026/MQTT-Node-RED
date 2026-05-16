# Remote Light Control using MQTT, Node-RED, AWS EC2, and Python

## Overview

This project demonstrates bidirectional IoT communication using:

* AWS EC2
* Mosquitto MQTT Broker
* Node-RED Dashboard
* Python MQTT Subscriber

The Node-RED dashboard acts as a remote control panel.

When a dashboard button is pressed:

* Node-RED publishes MQTT commands
* Python running on a local machine receives the commands
* The local machine simulates turning a light ON/OFF

This demonstrates a real cloud-to-device IoT control architecture.

---

# System Architecture

```text
Node-RED Dashboard Button
            ↓ MQTT
AWS EC2 Public IP
            ↓
Mosquitto MQTT Broker
            ↓
Python Subscriber (Local Machine)
            ↓
Simulated Light Control
```

---

# Features

* Bidirectional MQTT communication
* Cloud-to-device control
* Remote light ON/OFF simulation
* Node-RED control dashboard
* Python MQTT subscriber
* Real-time command handling

---

# Technologies Used

* AWS EC2 (Ubuntu)
* Mosquitto MQTT Broker
* Node-RED
* Python
* MQTT Protocol
* paho-mqtt
* node-red-dashboard

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

# MQTT Topic

This project uses:

```text
home/light
```

---

# Create Node-RED Control Flow

## Flow Structure

```text
[Light ON ] ─┐
             ├→ mqtt out
[Light OFF] ─┘
```

---

# Create ON Button

Add an Inject node.

Configure:

| Field        | Value    |
| ------------ | -------- |
| Payload Type | string   |
| Payload      | ON       |
| Name         | Light ON |

---

# Create OFF Button

Add another Inject node.

Configure:

| Field        | Value     |
| ------------ | --------- |
| Payload Type | string    |
| Payload      | OFF       |
| Name         | Light OFF |

---

# Configure MQTT Output Node

Add an MQTT Out node.

Configure:

| Field  | Value      |
| ------ | ---------- |
| Server | localhost  |
| Port   | 1883       |
| Topic  | home/light |

---

# Add Debug Node

Connect a Debug node to MQTT output.

Purpose:

* Monitor outgoing MQTT commands

---

# Deploy Flow

Click:

```text
Deploy
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

# Create Python MQTT Subscriber

Create:

```text
light_subscriber.py
```

Paste:

```python
import paho.mqtt.client as mqtt

BROKER = "YOUR_EC2_PUBLIC_IP"
PORT = 1883
TOPIC = "home/light"

def on_connect(client, userdata, flags, rc):
    print("Connected to MQTT Broker")
    client.subscribe(TOPIC)

def on_message(client, userdata, msg):

    command = msg.payload.decode()

    print(f"Received Command: {command}")

    if command == "ON":
        print("💡 LIGHT TURNED ON")

    elif command == "OFF":
        print("🌑 LIGHT TURNED OFF")

client = mqtt.Client()

client.on_connect = on_connect
client.on_message = on_message

print("Connecting...")
client.connect(BROKER, PORT, 60)

client.loop_forever()
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

# Run Python Subscriber

Open terminal inside project folder:

```powershell
python light_subscriber.py
```

Expected:

```text
Connecting...
Connected to MQTT Broker
```

---

# Test Remote Control

## Turn Light ON

Click:

```text
Light ON
```

Expected Python Output:

```text
Received Command: ON
💡 LIGHT TURNED ON
```

---

## Turn Light OFF

Click:

```text
Light OFF
```

Expected Python Output:

```text
Received Command: OFF
🌑 LIGHT TURNED OFF
```

---

# What This Project Demonstrates

This project demonstrates:

* MQTT Publish/Subscribe architecture
* Cloud-to-device communication
* Remote IoT control systems
* Bidirectional IoT communication
* Real-time command handling

---

# Real-World Applications

This architecture is used in:

* Smart homes
* Industrial automation
* Remote robotics
* Smart appliances
* IoT automation systems
* Cloud-controlled devices

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

* Control real LEDs using Raspberry Pi GPIO
* Control ESP32 relays
* Build mobile control dashboard
* Add MQTT authentication
* Add HTTPS security
* Create automation rules
* Store device logs in database
* Add voice assistant integration

---

# Final Result

You successfully built a cloud-based remote IoT control system using:

* AWS EC2
* Mosquitto MQTT Broker
* Node-RED Dashboard
* Python MQTT Subscriber
* Real-time MQTT Communication

---

# License

This project is intended for educational and learning purposes.
