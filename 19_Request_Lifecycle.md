# 19_Request_Lifecycle.md

# Request Lifecycle & Execution Flow

## Overview

This document explains the actual execution flow of the CNC Digital Twin project based on the repository implementation.

Unlike a traditional CRUD application, this system is primarily an event-driven Industrial IoT platform where telemetry flows through MQTT, AI processing, MongoDB persistence, Socket.IO broadcasting, and finally into a React dashboard.

Understanding this lifecycle is critical for interviews because it demonstrates how the entire system works end-to-end.

---

# System Execution Flow

The application contains four major runtime components:

```
Telemetry Simulator

↓

MQTT Broker (HiveMQ)

↓

Node.js Backend

↓

React Dashboard
```

Parallel to this:

```
Telemetry Simulator

↓

MQTT Broker

↓

AI Watchdog

↓

MQTT Alerts
```

---

# Complete Runtime Architecture

```
Industrial Telemetry Dataset
            │
            ▼
     dataset_replay.py
            │
            ▼
      HiveMQ Broker
       │         │
       │         │
       ▼         ▼
 Node Backend   AI Watchdog
       │         │
       │         ▼
       │   Alert Topic
       │         │
       └────┬────┘
            ▼
      Socket.IO
            │
            ▼
     React Dashboard
```

---

# Application Startup Sequence

## Step 1 — Backend Starts

File

```javascript
backend/server.js
```

Execution

```javascript
const express = require('express');
const http = require('http');
const { Server } = require('socket.io');
```

Responsibilities

- Create Express server
- Create HTTP server
- Initialize Socket.IO
- Connect MongoDB
- Connect MQTT broker

---

## Step 2 — MongoDB Connection

Server startup executes:

```javascript
mongoose.connect(process.env.MONGO_URI)
```

Purpose

- Connect MongoDB Atlas
- Create telemetry collection
- Enable history storage

Console

```
Connected to MongoDB Atlas!
```

---

## Step 3 — MQTT Connection

Backend connects to:

```javascript
mqtt://broker.hivemq.com:1883
```

Subscribes to:

```text
griet/cnc/telemetry

griet/cnc/alerts
```

Purpose

- Receive telemetry
- Receive AI alerts

---

## Step 4 — React Dashboard Starts

File

```typescript
frontend/src/main.tsx
```

Flow

```
main.tsx

↓

App.tsx

↓

Dashboard Rendered
```

Dashboard establishes:

```typescript
const socket = io(BACKEND_URL);
```

Purpose

- Create real-time Socket.IO connection

---

## Step 5 — AI Watchdog Starts

File

```python
ml/anomaly_detector.py
```

Connects to:

```python
broker.hivemq.com
```

Subscribes:

```python
griet/cnc/telemetry
```

Purpose

- Monitor incoming telemetry
- Detect anomalies
- Publish alerts

---

# Telemetry Lifecycle

The telemetry flow is the most important lifecycle in the project.

---

## Stage 1 — Telemetry Generation

Source

```python
dataset_replay.py
```

Data originates from

```text
laser_telemetry_dataset.csv
```

Flow

```
CSV Dataset

↓

dataset_replay.py

↓

MQTT Publish
```

Published Topic

```text
griet/cnc/telemetry
```

---

## Stage 2 — MQTT Broker

HiveMQ acts as message broker.

```
Publisher

↓

HiveMQ

↓

Subscribers
```

Subscribers

- Node.js Backend
- AI Watchdog

This creates loose coupling.

---

## Stage 3 — Backend Receives Telemetry

File

```javascript
server.js
```

Code Flow

```javascript
client.on('message')
```

Topic

```text
griet/cnc/telemetry
```

Process

```javascript
processTelemetry(payload)
```

---

# processTelemetry() Lifecycle

Function

```javascript
async function processTelemetry(payload)
```

Responsibilities

- Add anomaly state
- Broadcast telemetry
- Persist data

Flow

```
Payload

↓

Add is_anomaly

↓

Socket Broadcast

↓

MongoDB Save
```

---

## Real-Time Broadcast

Code

```javascript
io.emit('telemetry_stream', payload);
```

Purpose

Immediately update dashboard.

Benefit

No polling required.

---

## Database Persistence

Code

```javascript
Telemetry.create(...)
```

Stored Fields

```text
serial_no

status

temp

accel_x

accel_y

air_gas

timestamp

is_anomaly
```

Purpose

- Historical charts
- Trend analysis
- Replay

---

# AI Lifecycle

The AI pipeline executes independently.

---

## AI Receives Telemetry

File

```python
anomaly_detector.py
```

Flow

```python
on_message()
```

Input

```json
{
  "temp": 42,
  "accel_x": 0.2,
  "accel_y": 0.1,
  "air_gas": 0.8
}
```

---

## Hybrid Detection Engine

Function

```python
check_hybrid_fault()
```

Contains two layers.

---

### Layer 1 — Rule-Based Detection

Checks:

```python
temp > 50

acceleration > 1.8

air_gas < 0.6
```

Examples

```
Laser Overheating

Assist Gas Drop

Violent Head Motion
```

---

### Layer 2 — Isolation Forest

Model

```python
IsolationForest
```

Features

```python
temp

accel_x

accel_y

air_gas
```

Purpose

Detect unusual machine behavior not captured by thresholds.

---

## Alert Generation

When anomaly detected:

```python
client.publish(
    TOPIC_ALERTS
)
```

Topic

```text
griet/cnc/alerts
```

Payload

```json
{
  "type":"RED_ALERT",
  "score":0.82,
  "reason":"Assist Gas Pressure Drop"
}
```

---

# Alert Lifecycle

Backend also subscribes to:

```text
griet/cnc/alerts
```

When alert arrives:

```javascript
latestMLAlert = true;
```

Then:

```javascript
io.emit('ml_alert', payload);
```

Purpose

Push alert instantly to dashboard.

---

# Frontend Lifecycle

File

```typescript
App.tsx
```

---

## Historical Data Fetch

On application load:

```typescript
fetch('/api/history')
```

Backend Route

```javascript
GET /api/history
```

Database Query

```javascript
Telemetry.find()
```

Returns

Last 50 telemetry records.

Purpose

Populate charts immediately.

---

## Socket Event Processing

Frontend listens:

```typescript
socket.on(
    'telemetry_stream'
)
```

Flow

```
Socket Event

↓

Parse Payload

↓

Update React State

↓

Re-render Charts
```

---

# Dashboard Update Lifecycle

Telemetry received.

Updates:

```typescript
setCurrentTemp()

setCurrentAccel()

setCurrentGas()

setStatus()
```

Purpose

Update live machine state.

---

# Health Calculation Lifecycle

File

```typescript
App.tsx
```

Logic

```typescript
if(maxAccel > 1.8)
```

or

```typescript
gasLevel < 0.6
```

Then

```typescript
toolHealth--
```

Displayed As

```text
Laser Source Health
```

---

# OEE Lifecycle

Frontend maintains:

```typescript
oeeScore
```

Derived from:

```text
Uptime

Downtime

Anomaly Events
```

Purpose

Operational efficiency visualization.

---

# Uptime Tracking Lifecycle

When telemetry arrives:

```typescript
currentlyAnomaly
```

If anomaly:

```typescript
totalDowntimeSecs++
```

Else:

```typescript
totalUptimeSecs++
```

Result

```
Running Timeline

↓

Downtime Timeline

↓

OEE Metrics
```

---

# History API Lifecycle

Client

```http
GET /api/history
```

Backend

```javascript
Telemetry.find()
.sort({timestamp:-1})
.limit(50)
```

Database

```text
MongoDB Atlas
```

Response

```json
[
  {
    "temp":42,
    "air_gas":0.81
  }
]
```

---

# MongoDB Time-Series Lifecycle

Schema

```javascript
timeseries: {
 timeField:'timestamp',
 metaField:'serial_no'
}
```

Benefits

- Faster telemetry queries
- Reduced storage
- Better aggregation

---

# Socket.IO Lifecycle

Connection

```typescript
io(BACKEND_URL)
```

Backend

```javascript
new Server(server)
```

Events

### Server → Client

```text
telemetry_stream

ml_alert
```

Purpose

Real-time monitoring.

---

# End-to-End Request Lifecycle

## Telemetry Flow

```
laser_telemetry_dataset.csv

↓

dataset_replay.py

↓

MQTT Publish

↓

HiveMQ

↓

Node Backend

↓

processTelemetry()

↓

MongoDB

↓

Socket.IO

↓

React Dashboard
```

---

## Alert Flow

```
Telemetry

↓

AI Watchdog

↓

Isolation Forest

↓

MQTT Alert

↓

Backend

↓

Socket.IO

↓

Dashboard Alert
```

---

# Interview Talking Points

When explaining the project:

### "How does data enter the system?"

Answer:

```
dataset_replay.py

↓

MQTT

↓

Node.js Backend
```

---

### "How is AI integrated?"

Answer:

```
AI Watchdog

↓

Isolation Forest

↓

MQTT Alerts
```

---

### "How does the dashboard update instantly?"

Answer:

```
Backend

↓

Socket.IO

↓

React State

↓

Chart Re-render
```

---

### "Why MQTT?"

Answer:

- Decouples producers and consumers
- Lightweight protocol
- Widely used in Industrial IoT
- Supports multiple subscribers

---

### "Why MongoDB Time-Series Collections?"

Answer:

- Optimized for telemetry
- Faster range queries
- Lower storage overhead
- Native time-series support

---

# Summary

The CNC Digital Twin project follows an event-driven Industrial IoT architecture. Telemetry is generated by a simulator, streamed through MQTT, processed by a Node.js backend, analyzed by a Python AI watchdog using Isolation Forest, stored in MongoDB Atlas, and visualized in real time through Socket.IO-powered React dashboards. This architecture demonstrates full-stack engineering across IoT, backend systems, machine learning, databases, and real-time web applications.