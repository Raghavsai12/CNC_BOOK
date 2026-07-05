# Chapter 04 — System Architecture

# CNC Digital Twin System Architecture

---

# Executive Summary

The CNC Digital Twin follows an event-driven, distributed architecture designed to simulate a modern Industrial Internet of Things (IIoT) monitoring platform.

Rather than operating as a monolithic application, the system is divided into multiple independent services that communicate through MQTT and WebSockets.

Each component performs a single responsibility:

- Telemetry Generation
- Message Distribution
- Backend Processing
- Database Storage
- AI Anomaly Detection
- Dashboard Visualization

This separation improves modularity, maintainability, and scalability while closely resembling real industrial software architectures.

---

# 1. High-Level Architecture

```
                    Industrial Telemetry Dataset
                               │
                               ▼
                 Industrial Telemetry Simulator
                         (dataset_replay.py)
                               │
                               │ MQTT Publish
                               ▼
                      HiveMQ MQTT Broker
                     (broker.hivemq.com)
                     ┌──────────┴──────────┐
                     │                     │
                     ▼                     ▼
          Node.js Backend         Hybrid AI Watchdog
             (server.js)      (anomaly_detector.py)
                     │                     │
                     │                     │
          MongoDB Atlas         MQTT Alert Publisher
                     │                     │
                     └──────────┬──────────┘
                                ▼
                     React Dashboard
                    (Socket.IO + REST)
```

---

# 2. Architectural Style

The project combines several architectural patterns.

## Event-Driven Architecture

Telemetry is published as events instead of direct function calls.

Advantages:

- Loose coupling
- Independent services
- Easy scalability
- Fault isolation

---

## Publish–Subscribe Pattern

MQTT acts as a central broker.

Publishers never communicate directly with subscribers.

Instead:

Publisher

↓

Broker

↓

Subscribers

Current subscribers:

- Backend
- AI Watchdog

Future subscribers could include:

- Notification Service
- Grafana
- Analytics Engine
- Kafka Connector
- ERP Integration

---

## Client–Server Architecture

The dashboard communicates with the backend using:

- REST APIs
- Socket.IO

The frontend never accesses MongoDB directly.

---

## Layered Architecture

The project can also be viewed as multiple logical layers.

Presentation Layer

↓

Application Layer

↓

Messaging Layer

↓

Data Layer

↓

Machine Learning Layer

---

# 3. Major Components

The system consists of six primary components.

---

## Component 1 — Industrial Telemetry Simulator

File:

```
ml/dataset_replay.py
```

Responsibilities:

- Read telemetry dataset
- Publish MQTT messages
- Simulate live machine behavior

Outputs:

MQTT telemetry

Depends on:

- CSV Dataset
- HiveMQ

---

## Component 2 — MQTT Broker

HiveMQ Public Broker

Responsibilities:

- Receive published telemetry
- Distribute messages
- Decouple services

The broker contains no business logic.

It only routes messages.

---

## Component 3 — Backend

File:

```
backend/server.js
```

Responsibilities:

- Subscribe to MQTT
- Persist telemetry
- Broadcast live updates
- Serve REST APIs

Inputs:

MQTT telemetry

Outputs:

Socket.IO

REST API

MongoDB

---

## Component 4 — Database

MongoDB Atlas

Responsibilities:

- Store telemetry
- Store historical data
- Support dashboard queries

---

## Component 5 — Hybrid AI Watchdog

File:

```
ml/anomaly_detector.py
```

Responsibilities:

- Subscribe to telemetry
- Evaluate industrial rules
- Train Isolation Forest
- Detect anomalies
- Publish alerts

Outputs:

MQTT Alerts

---

## Component 6 — React Dashboard

Responsibilities:

- Display telemetry
- Display charts
- Display alerts
- Display historical data

Communication:

REST

+

Socket.IO

---

# 4. Communication Protocols

The project uses multiple protocols.

---

## MQTT

Used between:

Simulator

↓

Broker

↓

Backend

↓

AI

Reason:

Low-latency asynchronous messaging.

---

## HTTP

Used for:

Historical data retrieval.

Frontend

↓

Backend

↓

MongoDB

---

## WebSocket (Socket.IO)

Used for:

Live dashboard updates.

Backend

↓

Dashboard

Reason:

Bidirectional communication.

---

# 5. Data Storage Strategy

Telemetry follows two paths.

Real-Time Path

Telemetry

↓

MQTT

↓

Backend

↓

Socket.IO

↓

Dashboard

Historical Path

Telemetry

↓

MQTT

↓

Backend

↓

MongoDB

↓

REST API

↓

Dashboard

This separation allows live updates while preserving historical analytics.

---

# 6. AI Processing Strategy

The AI service is completely independent.

Instead of being embedded into the backend, it subscribes directly to MQTT.

Advantages:

- Independent deployment
- Easier scaling
- Better fault isolation
- Cleaner architecture

---

# 7. Failure Isolation

One of the strengths of the architecture is service independence.

If the AI service stops:

- Dashboard continues working.
- Backend continues storing telemetry.

If MongoDB becomes unavailable:

- Live dashboard continues.
- Historical queries fail.

If the frontend crashes:

- Backend continues processing telemetry.

This isolation improves system resilience.

---

# 8. Scalability

Current architecture supports future expansion.

Possible additions include:

- Multiple CNC machines
- Multiple MQTT topics
- Kafka
- Redis
- Authentication Service
- Notification Service
- Kubernetes deployment

Minimal changes would be required due to loose coupling.

---

# 9. Design Principles

The architecture follows several software engineering principles.

Single Responsibility

Each service performs one task.

Loose Coupling

Services communicate using MQTT.

High Cohesion

Related logic remains together.

Separation of Concerns

Frontend, Backend, AI, and Database remain independent.

Extensibility

New services can subscribe without modifying publishers.

---

# 10. Current Limitations

Current implementation:

Single backend

Single AI service

Single database

Public MQTT broker

No authentication

No service discovery

No message persistence

No load balancing

These are acceptable for a portfolio implementation.

---

# 11. Production Architecture

A production deployment would likely include:

Industrial PLC

↓

Edge Gateway

↓

Private MQTT Broker

↓

Kafka

↓

Backend Cluster

↓

Redis

↓

ML Service

↓

MongoDB Cluster

↓

Grafana

↓

Operator Dashboard

↓

Alerting Service

↓

ERP / MES

The current project demonstrates the same architectural concepts using a simplified deployment.

---

# Chapter Summary

The CNC Digital Twin uses an event-driven distributed architecture that separates telemetry generation, messaging, backend processing, machine learning, storage, and visualization into independent services.

This modular design improves maintainability, scalability, and fault isolation while reflecting architectural patterns commonly used in Industrial IoT systems.

The following chapter performs a complete reverse engineering of the backend service, beginning with application startup and continuing through MQTT integration, Socket.IO communication, REST APIs, MongoDB interaction, and overall execution flow.