# Chapter 06 — Complete Execution Flow

# End-to-End Execution Flow

---

# Executive Summary

The CNC Digital Twin consists of multiple independent services that collaborate to process industrial telemetry.

Unlike a traditional monolithic application, no single application performs every task.

Instead, telemetry flows through multiple layers:

- Telemetry Generation
- Message Broker
- Backend Processing
- Database Persistence
- Machine Learning
- Dashboard Visualization

Each service performs one responsibility before passing the data to the next service.

---

# Complete System Flow

```
Industrial Telemetry Dataset
        │
        ▼
dataset_replay.py
        │
        │ MQTT Publish
        ▼
HiveMQ Broker
   │             │
   │             │
   ▼             ▼
Node Backend   AI Watchdog
   │             │
   │             │
MongoDB       MQTT Alerts
   │             │
   └──────┬──────┘
          ▼
Socket.IO + REST API
          │
          ▼
React Dashboard
```

---

# Step 1 — Dataset Loading

File:

```
ml/dataset_replay.py
```

Responsibilities:

- Open laser_telemetry_dataset.csv
- Read every row
- Convert CSV row into Python dictionary
- Serialize into JSON
- Wait configured interval
- Publish over MQTT

Output:

```
Telemetry JSON
```

---

# Step 2 — MQTT Publish

Each telemetry record is published to:

```
griet/cnc/telemetry
```

MQTT Broker receives the message.

The publisher does not know who receives it.

This creates loose coupling.

---

# Step 3 — MQTT Broker

HiveMQ immediately forwards the telemetry to all subscribers.

Current subscribers:

- Backend
- Hybrid AI Watchdog

The broker performs no computation.

Its only responsibility is message routing.

---

# Step 4 — Backend Receives Telemetry

The backend MQTT client receives the JSON payload.

Responsibilities:

- Parse JSON
- Validate message
- Store telemetry
- Emit Socket.IO event

Output:

- MongoDB write
- Live dashboard update

---

# Step 5 — MongoDB Storage

The backend inserts telemetry into MongoDB Atlas.

Purpose:

- Historical analytics
- Dashboard refresh
- Trend visualization
- Long-term storage

This operation occurs independently of the dashboard.

---

# Step 6 — Socket.IO Broadcast

Immediately after receiving telemetry:

Backend

↓

Socket.IO

↓

React Dashboard

The browser receives telemetry without refreshing the page.

---

# Step 7 — Dashboard Update

React receives the Socket.IO event.

The UI updates:

- Temperature
- Acceleration
- Gas Pressure
- Charts
- Machine Status
- OEE
- Latest Telemetry

No polling is required.

---

# Step 8 — AI Watchdog

The AI service independently receives the same telemetry from MQTT.

Responsibilities:

- Add telemetry to rolling buffer
- Apply rule-based checks
- Train Isolation Forest
- Evaluate anomaly score
- Determine machine health

If abnormal:

Publish MQTT Alert

---

# Step 9 — Alert Processing

AI publishes:

```
griet/cnc/alerts
```

Backend subscribes to alerts.

Backend forwards alerts to the dashboard.

Dashboard displays:

- Alert Type
- Timestamp
- Severity
- Reason

---

# Step 10 — Historical Data Request

When the user requests history:

React

↓

HTTP GET

↓

Express

↓

MongoDB

↓

JSON Response

↓

Charts

Unlike live telemetry, historical data uses REST APIs.

---

# Live Data Flow

```
Simulator
     │
     ▼
 MQTT
     ▼
 Backend
     ▼
 Socket.IO
     ▼
 Dashboard
```

Latency target:

Near real-time.

---

# Historical Data Flow

```
Simulator
     │
     ▼
 MQTT
     ▼
 Backend
     ▼
 MongoDB
     ▼
 REST API
     ▼
 Dashboard
```

---

# AI Flow

```
Telemetry

↓

Rolling Buffer

↓

Rule Engine

↓

Isolation Forest

↓

Decision

↓

Alert

↓

MQTT

↓

Dashboard
```

---

# Communication Matrix

| Component | Protocol | Purpose |
|------------|----------|----------|
| Simulator → Broker | MQTT | Publish telemetry |
| Broker → Backend | MQTT | Telemetry delivery |
| Broker → AI | MQTT | Telemetry delivery |
| Backend → MongoDB | MongoDB Driver | Storage |
| Backend → Dashboard | Socket.IO | Live updates |
| Dashboard → Backend | HTTP | Historical queries |
| AI → Broker | MQTT | Alert publishing |
| Broker → Backend | MQTT | Alert delivery |
| Backend → Dashboard | Socket.IO | Alert updates |

---

# Event Sequence

1. Simulator starts

2. Dataset loaded

3. MQTT connection established

4. Telemetry published

5. Broker distributes message

6. Backend stores telemetry

7. Dashboard updated

8. AI evaluates telemetry

9. Alert published (if required)

10. Dashboard displays alert

---

# Failure Scenarios

## MQTT Broker Offline

Effect:

- No telemetry
- No alerts
- Dashboard freezes

Recovery:

Reconnect MQTT client.

---

## MongoDB Offline

Effect:

- Live dashboard still works
- Historical queries fail

---

## AI Service Offline

Effect:

- Telemetry continues
- Dashboard continues
- No anomaly detection
- No alerts

---

## Dashboard Closed

Effect:

Backend continues processing telemetry.

No data loss.

---

# Advantages of This Flow

- Event-driven
- Loosely coupled
- Modular
- Easy to scale
- Fault tolerant
- Cloud deployable

---

# Current Limitations

- Single machine
- Single MQTT topic
- Public broker
- No authentication
- No message persistence
- Single backend instance

---

# Chapter Summary

The CNC Digital Twin processes telemetry through a distributed event-driven pipeline.

Each service performs a dedicated responsibility, enabling real-time visualization, historical storage, and hybrid anomaly detection while remaining loosely coupled through MQTT.

This execution flow forms the foundation for understanding the backend, frontend, and AI components described in subsequent chapters.
