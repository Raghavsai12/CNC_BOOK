# 09_Backend_Architecture.md

# Backend Architecture

## Overview

The backend is the central processing layer of the CNC Digital Twin platform. It is responsible for receiving telemetry from CNC machines, processing real-time events, exposing REST APIs, managing authentication, streaming live updates via WebSockets, storing historical machine data, and serving AI-driven analytics.

The backend is designed using a modular, scalable, and event-driven architecture that supports high-frequency IoT data ingestion while maintaining low-latency communication with the frontend.

---

# Technology Stack

| Layer | Technology |
|--------|------------|
| Runtime | Node.js |
| Framework | Express.js |
| Language | JavaScript (ES6+) |
| Authentication | JWT |
| Database | MongoDB |
| ODM | Mongoose |
| Realtime | Socket.IO |
| Validation | express-validator |
| Logging | Morgan |
| Environment | dotenv |
| API Testing | Postman |
| Deployment | Docker + Nginx |

---

# High Level Architecture
Frontend (React)
                   │
         REST APIs / WebSocket
                   │
           Express Application
                   │
 ┌─────────────────┼─────────────────┐
 │                 │                 │
 Authentication Machine API Analytics API
│ │ │
└──────────────┬──┴─────────────────┘
│
Service Layer
│
┌──────────────┼──────────────┐
│ │ │
Machine Service Alert Service Analytics
│ │ │
└──────────────┼──────────────┘
│
MongoDB Database
│
Historical Data


---

# Backend Folder Structure


backend/
│
├── config/
│ database.js
│ socket.js
│ jwt.js
│
├── controllers/
│ authController.js
│ machineController.js
│ telemetryController.js
│ analyticsController.js
│
├── middleware/
│ auth.js
│ errorHandler.js
│ validation.js
│
├── models/
│ User.js
│ Machine.js
│ Telemetry.js
│ Alert.js
│
├── routes/
│ authRoutes.js
│ machineRoutes.js
│ telemetryRoutes.js
│ analyticsRoutes.js
│
├── services/
│ machineService.js
│ telemetryService.js
│ analyticsService.js
│ alertService.js
│
├── sockets/
│ socketHandler.js
│
├── utils/
│ logger.js
│ helpers.js
│
├── app.js
└── server.js


---

# Request Lifecycle


Client Request
│
▼
Express Router
│
▼
Authentication Middleware
│
▼
Validation Middleware
│
▼
Controller
│
▼
Business Service
│
▼
Database
│
▼
Controller Response
│
▼
JSON Response


---

# Core Backend Modules

## Authentication Module

Responsibilities

- User login
- User registration
- JWT generation
- Password hashing
- Token verification
- Role authorization

---

## Machine Module

Responsibilities

- Register machines
- Update machine details
- Machine status
- Machine configuration
- Machine health

---

## Telemetry Module

Responsibilities

- Receive sensor data
- Store historical telemetry
- Stream realtime values
- Data validation
- Timestamp synchronization

---

## Analytics Module

Responsibilities

- Production statistics
- Downtime analysis
- OEE calculations
- Machine utilization
- Trend generation

---

## Alert Module

Responsibilities

- Threshold monitoring
- Fault detection
- Alarm generation
- Notification creation

---

# REST API Design

## Authentication


POST /api/auth/login
POST /api/auth/register
POST /api/auth/logout
GET /api/auth/profile


---

## Machine APIs


GET /api/machines
GET /api/machines/:id
POST /api/machines
PUT /api/machines/:id
DELETE /api/machines/:id


---

## Telemetry APIs


POST /api/telemetry
GET /api/telemetry/:machineId
GET /api/telemetry/history


---

## Analytics APIs


GET /api/analytics/dashboard
GET /api/analytics/oee
GET /api/analytics/trends


---

# WebSocket Architecture

Events

Client → Server


join_machine

leave_machine

subscribe

unsubscribe


Server → Client


machine_update

telemetry

machine_alert

machine_status

dashboard_update


---

# Database Interaction

Backend communicates with MongoDB using Mongoose.

Flow


Controller

↓

Service

↓

Mongoose Model

↓

MongoDB

↓

JSON Result


---

# Security

Authentication

- JWT

Authorization

- RBAC

Password

- bcrypt hashing

API Security

- Helmet
- CORS
- Input validation

Rate Limiting

- express-rate-limit

Environment Variables


JWT_SECRET

MONGO_URI

PORT

CLIENT_URL


---

# Error Handling

Global Error Handler


400 Bad Request

401 Unauthorized

403 Forbidden

404 Not Found

409 Conflict

500 Internal Server Error


Every API returns


{
success,
message,
data,
error
}


---

# Logging

Application logs

- API requests
- Login attempts
- Errors
- Machine events
- Database errors
- Socket events

---

# Performance Optimizations

- Async operations
- Connection pooling
- Pagination
- MongoDB indexing
- Response compression
- Socket event batching

---

# Scalability

Current


Single Node.js Server


Future


Load Balancer

↓

Multiple Node Servers

↓

Redis Pub/Sub

↓

MongoDB Replica Set


---

# Deployment Architecture


Internet

↓

Nginx Reverse Proxy

↓

Express Server

↓

MongoDB

↓

Socket.IO

↓

Frontend


---

# Summary

The backend follows a modular layered architecture separating routing, controllers, services, business logic, and persistence. Real-time communication is handled using Socket.IO while MongoDB stores machine telemetry and historical analytics. The design emphasizes scalability, maintainability, and low-latency data processing suitable for industrial IoT and Digital Twin applications.