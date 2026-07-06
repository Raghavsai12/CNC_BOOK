# 20_Component_Deep_Dive.md

# Component Deep Dive

## Overview

This document provides a detailed walkthrough of the major components in the CNC Digital Twin project. Rather than describing the architecture at a high level, it focuses on how each major source file contributes to the overall system.

The project is composed of four primary runtime components:

```
React Frontend

↓

Node.js Backend

↓

Machine Learning Services

↓

MongoDB + MQTT Infrastructure
```

Each component has a clearly defined responsibility and communicates through well-defined interfaces.

---

# Component Overview

| Component | Responsibility |
|------------|----------------|
| `backend/server.js` | Backend server, REST APIs, MQTT subscriber, Socket.IO server |
| `frontend/src/main.tsx` | React application entry point |
| `frontend/src/App.tsx` | Dashboard UI, charts, Socket.IO client |
| `ml/dataset_replay.py` | Telemetry simulator and MQTT publisher |
| `ml/anomaly_detector.py` | AI watchdog and anomaly detection |
| MongoDB | Telemetry persistence |
| HiveMQ | MQTT message broker |

---

# Backend Component

## File

```
backend/server.js
```

---

## Primary Responsibility

Acts as the central orchestration layer of the application.

It is responsible for:

- Starting Express
- Creating HTTP server
- Initializing Socket.IO
- Connecting MongoDB
- Connecting HiveMQ
- Exposing REST APIs
- Processing telemetry
- Broadcasting live updates

---

## Internal Responsibilities

```
server.js

│

├── Express Configuration

├── MongoDB Connection

├── MQTT Client

├── Socket.IO Server

├── REST Routes

├── Telemetry Processing

└── Alert Processing
```

---

## Startup Flow

```
Node Starts

↓

Load Environment Variables

↓

Connect MongoDB

↓

Create Express App

↓

Create HTTP Server

↓

Initialize Socket.IO

↓

Connect MQTT Broker

↓

Listen on PORT
```

---

## MQTT Subscriber

The backend subscribes to:

```
griet/cnc/telemetry

griet/cnc/alerts
```

Purpose

- Receive live machine data
- Receive anomaly alerts

---

## REST API Layer

Current REST endpoints include:

```
GET /health

GET /api/history
```

Future endpoints can include:

```
POST /machines

GET /analytics

POST /alerts
```

---

## Socket.IO Server

The backend broadcasts:

```
telemetry_stream

ml_alert
```

Clients receive updates instantly.

---

## Telemetry Processor

Main responsibilities:

```
Receive MQTT Message

↓

Parse JSON

↓

Validate Payload

↓

Store MongoDB

↓

Broadcast Socket Event
```

---

# Frontend Entry Component

## File

```
frontend/src/main.tsx
```

---

## Purpose

Bootstraps the React application.

Responsibilities

- Create React root
- Render App component
- Attach application to browser DOM

Flow

```
main.tsx

↓

<App />

↓

Browser
```

---

# Dashboard Component

## File

```
frontend/src/App.tsx
```

---

## Responsibilities

Acts as the main dashboard.

Displays:

- Current telemetry
- Machine status
- OEE
- Alerts
- Historical charts
- Machine health
- Live metrics

---

## Internal Structure

```
App.tsx

│

├── Socket Connection

├── REST API Calls

├── State Management

├── Dashboard Cards

├── Charts

├── Alert Banner

└── Health Indicators
```

---

## State Variables

Examples include:

```
Current Temperature

Current Acceleration

Gas Pressure

Machine Status

Tool Health

OEE Score

Historical Data

Current Alerts
```

These values update automatically when socket events arrive.

---

## Socket.IO Client

Connection

```typescript
const socket = io(...)
```

Listeners

```
telemetry_stream

ml_alert
```

Purpose

Receive live backend updates.

---

## REST Communication

On application startup:

```
GET /api/history
```

Purpose

Load historical telemetry before live streaming begins.

---

## Rendering Strategy

```
State Changes

↓

React Reconciliation

↓

Virtual DOM

↓

DOM Updates
```

Only affected components are re-rendered.

---

# Telemetry Replay Component

## File

```
ml/dataset_replay.py
```

---

## Responsibility

Simulates a real CNC machine.

Reads telemetry from a dataset and publishes it to MQTT.

---

## Internal Workflow

```
CSV Dataset

↓

Read Row

↓

Convert JSON

↓

Publish MQTT

↓

Wait

↓

Repeat
```

---

## Purpose

Provides:

- Testing
- Demonstrations
- Dashboard validation
- AI evaluation

without requiring a physical CNC machine.

---

## MQTT Publisher

Publishes to:

```
griet/cnc/telemetry
```

Each published message represents one sensor reading.

---

# AI Component

## File

```
ml/anomaly_detector.py
```

---

## Responsibility

Acts as an intelligent monitoring service.

Receives telemetry independently from the backend and evaluates machine health.

---

## Internal Modules

```
MQTT Subscriber

↓

Feature Extraction

↓

Rule Engine

↓

Isolation Forest

↓

Alert Publisher
```

---

## Rule Engine

Checks conditions like:

```
Temperature

Acceleration

Assist Gas

Machine Status
```

Immediate anomalies generate alerts.

---

## Isolation Forest

Purpose

Detects unseen abnormal behavior using unsupervised machine learning.

Inputs

```
Temperature

Acceleration

Gas Pressure
```

Output

```
Normal

Anomaly
```

---

## Alert Publisher

Publishes

```
griet/cnc/alerts
```

Backend receives these alerts automatically.

---

# MongoDB Component

Purpose

Persistent storage for telemetry.

Stores:

```
Historical Telemetry

Machine Status

Anomaly State

Timestamp
```

---

## Why MongoDB?

Advantages

- Flexible schema
- Native JSON
- Time-series support
- Fast inserts
- Horizontal scaling

---

# MQTT Broker

Broker

```
HiveMQ
```

Purpose

Decouples producers and consumers.

Current publisher

```
dataset_replay.py
```

Subscribers

```
Node Backend

AI Watchdog
```

Benefits

- Loose coupling
- Low bandwidth
- High throughput
- Reliable messaging

---

# Socket.IO Component

Purpose

Real-time browser communication.

Current events

```
telemetry_stream

ml_alert
```

Benefits

- Instant updates
- No polling
- Reduced latency

---

# Component Communication Matrix

| Source | Destination | Protocol |
|----------|-------------|----------|
| Dataset Replay | HiveMQ | MQTT |
| HiveMQ | Backend | MQTT |
| HiveMQ | AI Watchdog | MQTT |
| Backend | MongoDB | Mongoose |
| Backend | React | Socket.IO |
| React | Backend | REST |
| AI Watchdog | HiveMQ | MQTT |

---

# Runtime Dependencies

```
React

↓

REST + Socket.IO

↓

Express

↓

MQTT

↓

MongoDB
```

Parallel

```
MQTT

↓

Python AI

↓

MQTT Alerts
```

---

# Failure Handling

## Backend Failure

Impact

- APIs unavailable
- Dashboard stops updating
- MQTT messages not processed

---

## MongoDB Failure

Impact

- Historical data unavailable
- Live updates may continue
- Telemetry persistence fails

---

## MQTT Failure

Impact

- No incoming telemetry
- AI stops receiving data
- Dashboard becomes static

---

## Frontend Failure

Impact

- Backend continues processing
- Database continues storing telemetry
- Users lose visualization only

---

## AI Failure

Impact

- Telemetry continues
- Dashboard continues
- Intelligent alerts unavailable

---

# Scalability Considerations

Current

```
Single Backend

Single AI Process

Single MQTT Broker
```

Future

```
Multiple Backend Instances

↓

Redis Adapter

↓

Kafka

↓

Multiple AI Workers

↓

Load Balancer
```

---

# Design Principles

The project follows several software engineering principles:

- Separation of concerns
- Modular architecture
- Event-driven communication
- Loose coupling through MQTT
- Single responsibility for each component
- Independent scalability of frontend, backend, and AI services

---

# Interview Talking Points

When discussing components:

- Explain that the backend is the orchestration layer connecting MQTT, MongoDB, REST APIs, and Socket.IO.
- Describe the frontend as a real-time dashboard driven by WebSocket events.
- Explain how `dataset_replay.py` simulates industrial telemetry.
- Discuss how `anomaly_detector.py` independently consumes telemetry and publishes alerts without tightly coupling to the backend.
- Highlight MQTT as the communication backbone enabling independent evolution of services.

---

# Summary

The CNC Digital Twin project is built from loosely coupled, independently deployable components. The backend orchestrates communication, the frontend visualizes machine state, the telemetry replay service simulates industrial equipment, and the AI watchdog performs anomaly detection. This modular architecture simplifies maintenance, improves scalability, and demonstrates best practices for Industrial IoT and AI-enabled monitoring systems.