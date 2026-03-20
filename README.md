# Dynamic Channel Allocation for IoT Networks Using Interference-Aware Wi-Fi

## Overview

This project implements a dynamic Wi-Fi channel allocation system for IoT networks using an interference-aware approach.

The system continuously monitors Wi-Fi interference across channels and intelligently switches to the optimal channel to improve latency, packet loss, and overall network performance.

It is designed using multiple ESP32-based components that simulate a real-world IoT communication environment.

---

## Objectives

* Reduce Wi-Fi interference in IoT networks
* Improve communication reliability (low latency and packet loss)
* Dynamically adapt to changing wireless environments
* Demonstrate intelligent channel selection using real-time data

---

## System Architecture

The system consists of three main components:

### 1. Access Point (Wi-Fi AP)

**Role:**
Creates and manages the Wi-Fi network.

**Details:**

* SSID: DynamicAP
* Password: password
* Default Channel: 1

**Key Features:**

* Provides endpoint to change channel dynamically
* Maintains current channel state
* Acts as the central communication hub

**Endpoints:**

* `/setchannel?channel=X` → Change Wi-Fi channel
* `/status` → Returns current channel

**Interaction:**

* Receives commands from the controller
* All devices connect through this AP

---

### 2. Central Controller

**Role:**
Acts as the brain of the system, making intelligent channel decisions.

**Key Features:**

#### Channel Scanning

* Scans channels 1–11 every 30 seconds
* Collects:

  * Number of APs
  * Signal strength (RSSI)

#### Interference Scoring Algorithm

Each channel is scored based on:

* AP count: +1 per AP
* Strong APs (RSSI > -70 dBm): +2 per AP
* Adjacent interference:

  * ±1 channel: 0.5 × AP count
  * ±2 channels: 0.25 × AP count
* Non-overlapping channels (1, 6, 11): score × 0.8

#### Decision Logic

* Selects best channel with lowest score
* Switches only if at least 20% improvement over current channel

#### Web Dashboard

Accessible at:

```
http://192.168.4.2/
```

Displays:

* Current channel
* Latency
* Packet loss
* Channel interference scores

(Auto-refresh every 5 seconds)

#### UDP Echo Server

* Port: 8888
* Echoes packets back to IoT node
* Used for RTT (latency) calculation

#### APIs

* `/api/node-metrics` → Receives IoT metrics
* `/api/status` → Provides real-time JSON data

**Interaction:**

* Sends channel switch commands to AP
* Communicates with IoT node (UDP and HTTP)
* Serves dashboard and APIs

---

### 3. IoT Node

**Role:**
Simulates an IoT device generating traffic and measuring network performance.

**Key Features:**

#### UDP Traffic Generation

* Sends packet every 1 second
* Includes:

  * Sequence number
  * Timestamp

#### Echo Handling

* Receives echoed packets
* Calculates Round-Trip Time (RTT)

#### Metrics Calculation (every 10 seconds)

* Average Latency
* Packet Loss (%)

#### HTTP Reporting

* Sends metrics to:

```
/api/node-metrics
```

#### Configuration

* Static IP: 192.168.4.3

**Interaction:**

* Sends UDP packets to controller
* Receives echoes
* Reports metrics via HTTP
* Communicates through AP

---

## Project Structure

```
project/
│── AP.txt
│── controller.txt
│── node.txt
│── Iot_Network.pptx
│── files/
└── README.md
```

---

## Setup and Usage

### 1. Upload Code

Flash the following files to respective ESP32 devices:

* AP.txt → Access Point
* controller.txt → Central Controller
* node.txt → IoT Node

### 2. Connect Devices

All devices connect to:

* SSID: DynamicAP
* Password: password

### 3. Run System

* Power all ESP32 devices
* Controller starts scanning automatically
* IoT node begins sending traffic
* AP switches channels dynamically

---

## Performance Metrics

The system evaluates:

* Latency (RTT)
* Packet Loss (%)
* Channel Interference Score

---

## Key Features

* Real-time adaptive channel selection
* Interference-aware decision making
* Live monitoring dashboard
* Lightweight IoT simulation
* Scalable architecture

---

## Future Enhancements

* Machine learning-based channel prediction
* Multi-node IoT scaling
* Mobile app dashboard
* Integration with real smart devices
* Support for 5 GHz channels

---

## Applications

* Smart homes
* Industrial IoT
* Smart agriculture
* Wireless sensor networks

