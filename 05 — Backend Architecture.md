# Chapter 05 — Backend Architecture

# Backend Service

Directory:

backend/

Main Entry Point:

server.js

Technology Stack:

- Node.js
- Express.js
- Socket.IO
- MQTT.js
- MongoDB Atlas
- Mongoose
- dotenv
- CORS

---

# 1. Purpose

The backend acts as the central orchestration service of the CNC Digital Twin.

Every subsystem either sends data to the backend or receives data from it.

Without the backend:

- No telemetry storage
- No REST APIs
- No dashboard updates
- No database communication

The backend acts as the bridge between the industrial messaging layer and the user interface.

---

# 2. Responsibilities

The backend performs several independent responsibilities simultaneously.

## Telemetry Ingestion

Receives telemetry from MQTT.

---

## Data Persistence

Stores incoming telemetry into MongoDB Atlas.

---

## Live Dashboard

Immediately forwards telemetry to connected browsers using Socket.IO.

---

## Historical Data

Provides REST endpoints for querying stored telemetry.

---

## Alert Forwarding

Receives AI alerts from MQTT.

Broadcasts alerts to all connected dashboard clients.

---

## Service Coordination

Coordinates communication between:

Telemetry Simulator

↓

MQTT Broker

↓

Backend

↓

MongoDB

↓

Socket.IO

↓

Dashboard

---

# 3. Why Node.js?

Node.js was selected because the backend is primarily I/O bound.

The backend spends most of its time:

- Waiting for MQTT messages
- Waiting for MongoDB
- Waiting for browser connections

Node's event loop efficiently handles thousands of concurrent I/O operations without requiring multiple threads.

---

# Alternatives

Python FastAPI

Java Spring Boot

Go

ASP.NET

---

Trade-offs

Advantages

✓ Fast development

✓ Large ecosystem

✓ Excellent MQTT support

✓ Excellent WebSocket support

✓ JSON native

Disadvantages

✗ Single-threaded CPU execution

✗ Not ideal for CPU-heavy workloads

---

# 4. Backend Architecture

```

Telemetry Simulator

↓

MQTT Broker

↓

server.js

├── MQTT Client

├── MongoDB

├── Socket.IO

├── Express

└── REST APIs

↓

Dashboard

```

---

# 5. Startup Sequence

Application startup follows this order.

1.

Load environment variables

↓

2.

Create Express application

↓

3.

Enable middleware

↓

4.

Create HTTP server

↓

5.

Attach Socket.IO

↓

6.

Connect MongoDB

↓

7.

Connect MQTT Broker

↓

8.

Subscribe to telemetry topics

↓

9.

Start HTTP server

↓

10.

Accept client connections

---

# 6. Express Server

Express is responsible for:

- REST APIs
- Middleware
- HTTP Routing

Express does NOT process telemetry directly.

Telemetry arrives through MQTT.

---

# 7. Middleware

Typical middleware includes:

CORS

Allows frontend communication.

Express JSON Parser

Parses incoming JSON requests.

Environment Variables

Loaded using dotenv.

---

# 8. Environment Variables

Sensitive configuration is stored outside the source code.

Typical variables include:

MONGO_URI

PORT

MQTT_BROKER

MQTT_PORT

This improves portability and security.

---

# 9. MQTT Integration

The backend creates an MQTT client.

Responsibilities:

- Connect Broker
- Subscribe Topics
- Receive Telemetry
- Receive Alerts

Topics include:

griet/cnc/telemetry

griet/cnc/alerts

The backend never polls for data.

Everything is event-driven.

---

# 10. Telemetry Processing Pipeline

Incoming MQTT Message

↓

Parse JSON

↓

Validate Payload

↓

Store MongoDB

↓

Emit Socket.IO Event

↓

Dashboard Updates

The backend performs minimal computation.

Heavy analytics are delegated to the AI service.

---

# 11. Socket.IO

Purpose

Provide live communication between backend and dashboard.

Whenever telemetry arrives:

Socket.IO broadcasts immediately.

Advantages

- Low latency

- Bidirectional

- Automatic reconnect

- Browser friendly

---

# 12. REST APIs

REST APIs provide historical information.

Unlike Socket.IO:

REST is request-response.

Typical workflow

Dashboard

↓

GET Request

↓

Express

↓

MongoDB

↓

JSON Response

---

# 13. MongoDB Integration

MongoDB stores telemetry for:

Historical charts

Machine history

Trend analysis

Dashboard reloads

Without MongoDB:

Dashboard would only display live telemetry.

Historical analytics would disappear.

---

# 14. Error Handling

The backend attempts to isolate failures.

MQTT Failure

Reconnect

MongoDB Failure

Continue live updates if possible.

Socket.IO Failure

Does not interrupt MQTT ingestion.

---

# 15. Event-Driven Design

Instead of sequential execution,

the backend reacts to events.

Examples:

MQTT Connected

↓

Subscribe Topics

Telemetry Received

↓

Store Database

↓

Emit Dashboard

Client Connected

↓

Send Current State

This architecture improves responsiveness.

---

# 16. Separation of Responsibilities

The backend intentionally avoids:

Machine Learning

Sensor Simulation

Frontend Rendering

Its responsibility is only coordination.

---

# 17. Performance Characteristics

Most operations are asynchronous.

Incoming telemetry does not block browser communication.

MongoDB operations execute independently.

Socket.IO broadcasts asynchronously.

Node.js event loop handles these efficiently.

---

# 18. Security Considerations

Current implementation:

Environment Variables

CORS

MongoDB Atlas authentication

Missing:

JWT Authentication

Authorization

HTTPS enforcement

Rate limiting

Input validation

Helmet

Request logging

These would be recommended for production.

---

# 19. Scalability

Current backend supports:

Single process

Single MQTT client

Single MongoDB connection

Future improvements:

Redis

Load balancing

Horizontal scaling

Microservices

Docker

Kubernetes

---

# 20. Strengths

✓ Simple

✓ Easy to understand

✓ Event-driven

✓ Low latency

✓ Modular communication

✓ Cloud deployable

---

# 21. Weaknesses

Current backend is implemented primarily inside one file:

server.js

As the application grows it should be refactored into:

controllers/

routes/

services/

mqtt/

database/

middleware/

config/

models/

utils/

This improves maintainability.

---

# 22. Production Improvements

Introduce:

Request validation

Logging

Metrics

Authentication

Authorization

Docker

CI/CD

Health endpoints

Redis caching

Structured configuration

---

# 23. Interview Talking Points

You should be able to explain:

Why MQTT instead of HTTP?

Why Socket.IO?

Why MongoDB?

Why Node.js?

How does telemetry flow?

Why event-driven architecture?

How are historical and live data separated?

How would you scale this backend?

How would you secure it?

---

# Chapter Summary

The backend is the central coordination layer of the CNC Digital Twin.

It connects the messaging layer, database, AI service, and frontend while maintaining an event-driven architecture.

Its primary responsibilities are telemetry ingestion, persistence, API serving, and real-time event broadcasting.

Subsequent chapters will reverse engineer every implementation inside server.js, including imports, functions, execution flow, REST endpoints, MQTT callbacks, Socket.IO events, and database operations.