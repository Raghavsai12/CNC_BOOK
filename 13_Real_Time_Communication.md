# 13_Real_Time_Communication.md

# Real-Time Communication Architecture

## Overview

The CNC Digital Twin platform relies on real-time communication to provide operators and engineers with live machine status, telemetry updates, alerts, and analytics. While REST APIs are used for CRUD operations and historical data retrieval, **WebSockets (Socket.IO)** are used for low-latency, bidirectional communication.

This hybrid communication model ensures that users always see the latest machine state without manually refreshing the application.

---

# Communication Architecture

```
                  CNC Machine
                       │
                Sensor Readings
                       │
                       ▼
                IoT Gateway/API
                       │
                HTTP / MQTT (Future)
                       │
                       ▼
              Express.js Backend
                       │
          ┌────────────┴────────────┐
          │                         │
      MongoDB                  Socket.IO Server
          │                         │
          └────────────┬────────────┘
                       │
                WebSocket Connection
                       │
                 React Frontend
```

---

# Why WebSockets?

Traditional HTTP polling introduces unnecessary latency and network overhead because the client repeatedly requests updates even when no new data exists.

WebSockets provide:

- Persistent connection
- Full-duplex communication
- Low latency
- Reduced bandwidth usage
- Real-time event delivery

This makes them well suited for industrial monitoring systems.

---

# Communication Methods

| Feature | REST API | WebSocket |
|----------|----------|-----------|
| Login | ✅ | ❌ |
| Register Machine | ✅ | ❌ |
| Fetch Machine List | ✅ | ❌ |
| Historical Telemetry | ✅ | ❌ |
| Live Telemetry | ❌ | ✅ |
| Live Alerts | ❌ | ✅ |
| Dashboard Updates | ❌ | ✅ |
| Machine Status | ❌ | ✅ |

---

# Connection Lifecycle

```
Frontend Loads

↓

Socket Connection Created

↓

Handshake

↓

Authentication

↓

Connection Established

↓

Join Machine Room

↓

Receive Live Events

↓

Disconnect

↓

Automatic Reconnection
```

---

# Socket.IO Architecture

```
React Client
      │
Socket.IO Client
      │
──────────────
Persistent Connection
──────────────
      │
Socket.IO Server
      │
Event Dispatcher
      │
Backend Services
```

---

# Connection Flow

```
Client Starts

↓

socket.connect()

↓

Handshake

↓

JWT Verification

↓

Connection Accepted

↓

Join Room

↓

Receive Updates
```

---

# Authentication During Connection

Before allowing a client to receive machine updates, the backend verifies the JWT.

```
Client

↓

Socket Connection

↓

JWT Token

↓

Backend Verification

↓

Accept / Reject Connection
```

Example

```javascript
io.use((socket, next) => {
    const token = socket.handshake.auth.token;

    // verify JWT

    next();
});
```

---

# Event-Driven Architecture

The backend publishes events whenever machine data changes.

```
Telemetry Received

↓

Telemetry Service

↓

Database

↓

Socket Event

↓

Frontend Dashboard
```

---

# Client Events

Events emitted by the frontend.

```
connect

disconnect

join_machine

leave_machine

subscribe_dashboard

unsubscribe_dashboard

request_latest_data
```

---

# Server Events

Events emitted by the backend.

```
telemetry_update

machine_update

machine_status

alert_created

alert_resolved

dashboard_update

analytics_update

system_notification
```

---

# Machine Room Architecture

Each CNC machine has its own Socket.IO room.

```
Socket Server

│

├── Room CNC-001

├── Room CNC-002

├── Room CNC-003

└── Dashboard Room
```

Benefits

- Efficient broadcasting
- Reduced network traffic
- Isolated updates
- Better scalability

---

# Join Machine Room

Example Flow

```
User Opens Machine

↓

Frontend Emits

join_machine

↓

Backend Adds Socket

↓

Room CNC-001
```

Example

```javascript
socket.emit("join_machine", "CNC-001");
```

---

# Leave Machine Room

```
User Changes Machine

↓

leave_machine

↓

Leave Previous Room

↓

Join New Room
```

---

# Example Telemetry Event

```json
{
    "machineId":"CNC-001",
    "rpm":3400,
    "temperature":69.8,
    "power":15.1,
    "vibration":0.03,
    "timestamp":"2026-07-05T11:20:00Z"
}
```

---

# Example Alert Event

```json
{
    "machineId":"CNC-001",
    "severity":"HIGH",
    "type":"Temperature",
    "message":"Temperature exceeded threshold"
}
```

---

# Dashboard Update Flow

```
Sensor Data

↓

Backend

↓

MongoDB

↓

Socket Event

↓

Redux Store

↓

React Components

↓

Updated Dashboard
```

---

# State Synchronization

The frontend keeps the latest machine state synchronized by:

- Receiving socket events
- Updating the global state
- Re-rendering only affected components

Flow

```
Socket Event

↓

Redux Dispatch

↓

Store Updated

↓

Component Re-render
```

---

# Reconnection Strategy

If the connection is lost:

```
Connection Lost

↓

Retry

↓

Reconnect

↓

Authenticate

↓

Rejoin Rooms

↓

Resume Updates
```

Socket.IO provides automatic reconnection with configurable retry intervals.

---

# Heartbeat Mechanism

Socket.IO periodically exchanges ping/pong messages.

```
Client Ping

↓

Server Pong

↓

Connection Alive
```

This helps detect broken or inactive connections.

---

# Error Handling

Possible socket errors:

- Authentication failure
- Connection timeout
- Network interruption
- Invalid event payload
- Unauthorized room access

Example

```javascript
socket.on("connect_error", (err) => {
    console.log(err.message);
});
```

---

# Performance Optimizations

The real-time communication layer is optimized by:

- Broadcasting only changed data
- Machine-specific rooms
- Event batching
- Payload compression
- Debouncing high-frequency updates
- Avoiding unnecessary re-renders
- Lazy subscription to inactive machines

---

# Scalability

Current Architecture

```
Single Socket.IO Server
```

Future Architecture

```
Load Balancer

↓

Multiple Socket.IO Servers

↓

Redis Adapter

↓

Shared Event Bus
```

The Redis adapter enables events to be shared across multiple backend instances.

---

# Security

Security measures include:

- JWT verification during handshake
- Role validation
- Event validation
- Room-level authorization
- Rate limiting
- TLS encryption (WSS)
- Input sanitization

---

# Monitoring

Metrics monitored include:

- Active socket connections
- Connected users
- Messages per second
- Average latency
- Failed connections
- Reconnection attempts
- Event processing time

---

# End-to-End Data Flow

```
CNC Machine

↓

Sensor Reading

↓

IoT Gateway

↓

Backend API

↓

Telemetry Service

↓

MongoDB

↓

Socket.IO

↓

React Frontend

↓

Redux Store

↓

Dashboard Components

↓

User Interface Updated
```

---

# Future Enhancements

- MQTT integration for industrial devices
- Apache Kafka for event streaming
- Redis Pub/Sub for distributed messaging
- Binary serialization (Protocol Buffers)
- Event replay for reconnecting clients
- Offline event buffering
- Edge gateway synchronization

---

# Interview Talking Points

If asked about real-time communication, explain:

- Why WebSockets are preferred over HTTP polling for live telemetry.
- The difference between REST APIs and WebSockets in the application.
- How Socket.IO rooms reduce unnecessary broadcasts.
- How automatic reconnection improves reliability.
- How Redux is updated from socket events.
- Why JWT authentication is performed during the WebSocket handshake.
- How the architecture scales using Redis and multiple Socket.IO servers.

---

# Summary

The CNC Digital Twin platform uses a hybrid communication architecture where REST APIs manage resource-based operations and Socket.IO delivers real-time telemetry, alerts, and dashboard updates. Machine-specific rooms, authenticated WebSocket connections, and efficient event broadcasting provide a scalable and responsive foundation for industrial monitoring and digital twin visualization.