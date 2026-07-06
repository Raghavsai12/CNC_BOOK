# 11_API_Architecture.md

# API Architecture

## Overview

The CNC Digital Twin platform exposes a RESTful API that enables communication between the frontend, backend services, IoT devices, and external systems. The API is designed with scalability, security, consistency, and maintainability in mind.

The API layer follows REST principles, uses JSON for data exchange, and integrates WebSocket communication for real-time telemetry updates.

---

# API Design Principles

The API architecture follows these principles:

- RESTful resource-based design
- Stateless communication
- JSON request and response format
- Consistent URL structure
- Proper HTTP status codes
- JWT-based authentication
- Role-based authorization
- Versioned APIs
- Input validation
- Standardized error responses

---

# Base URL

```
http://localhost:5000/api/v1
```

Production Example

```
https://api.cncdigitaltwin.com/api/v1
```

---

# API Architecture

```
                React Frontend
                      │
                Axios HTTP Client
                      │
               REST API Requests
                      │
          Express Router Layer
                      │
      Authentication Middleware
                      │
         Validation Middleware
                      │
             Controller Layer
                      │
              Service Layer
                      │
            MongoDB Database
```

---

# API Versioning

Current Version

```
v1
```

Future Versions

```
/api/v2

/api/v3
```

Versioning ensures backward compatibility while introducing new features.

---

# Authentication Flow

```
Client Login
      │
      ▼
POST /auth/login
      │
      ▼
Credentials Validation
      │
      ▼
JWT Generation
      │
      ▼
Client Stores Token
      │
      ▼
Authorization Header
      │
      ▼
Protected APIs
```

---

# Authentication APIs

## Register User

```
POST /auth/register
```

Request

```json
{
    "name":"John",
    "email":"john@example.com",
    "password":"password123"
}
```

Success Response

```json
{
    "success": true,
    "message": "User registered successfully"
}
```

---

## Login

```
POST /auth/login
```

Request

```json
{
    "email":"john@example.com",
    "password":"password123"
}
```

Success Response

```json
{
    "success": true,
    "token":"JWT_TOKEN",
    "user":{
        "id":"123",
        "name":"John",
        "role":"admin"
    }
}
```

---

## Get Profile

```
GET /auth/profile
```

Authorization Required

```
Bearer Token
```

---

## Logout

```
POST /auth/logout
```

---

# Machine APIs

## Get All Machines

```
GET /machines
```

Response

```json
[
  {
    "machineId":"CNC-001",
    "status":"RUNNING",
    "location":"Factory A"
  }
]
```

---

## Get Single Machine

```
GET /machines/:id
```

---

## Register Machine

```
POST /machines
```

Request

```json
{
    "machineId":"CNC-001",
    "name":"Vertical Milling Machine",
    "manufacturer":"Mazak",
    "model":"VCN-530C"
}
```

---

## Update Machine

```
PUT /machines/:id
```

---

## Delete Machine

```
DELETE /machines/:id
```

---

# Telemetry APIs

## Receive Telemetry

```
POST /telemetry
```

Request

```json
{
    "machineId":"CNC-001",
    "rpm":3200,
    "temperature":72.5,
    "vibration":0.02,
    "power":14.5,
    "timestamp":"2026-07-05T11:15:00Z"
}
```

---

## Get Latest Telemetry

```
GET /telemetry/latest/:machineId
```

---

## Get Historical Telemetry

```
GET /telemetry/history/:machineId
```

Query Parameters

```
startDate

endDate

limit

page
```

Example

```
GET /telemetry/history/CNC-001?page=1&limit=100
```

---

# Analytics APIs

## Dashboard Summary

```
GET /analytics/dashboard
```

Returns

- Machine Count
- Active Machines
- Downtime
- OEE
- Active Alerts

---

## OEE Metrics

```
GET /analytics/oee
```

---

## Trend Analysis

```
GET /analytics/trends
```

---

## Predictive Maintenance

```
GET /analytics/predictive
```

---

# Alert APIs

## Get Alerts

```
GET /alerts
```

---

## Active Alerts

```
GET /alerts/active
```

---

## Resolve Alert

```
PUT /alerts/:id
```

---

# Health Check API

```
GET /health
```

Example Response

```json
{
    "status":"UP",
    "database":"CONNECTED",
    "uptime":"99.98%"
}
```

---

# HTTP Status Codes

| Code | Meaning |
|------|----------|
| 200 | Success |
| 201 | Created |
| 204 | No Content |
| 400 | Bad Request |
| 401 | Unauthorized |
| 403 | Forbidden |
| 404 | Not Found |
| 409 | Conflict |
| 422 | Validation Failed |
| 429 | Too Many Requests |
| 500 | Internal Server Error |

---

# Standard Response Format

Success

```json
{
    "success":true,
    "message":"Operation successful",
    "data":{}
}
```

Failure

```json
{
    "success":false,
    "message":"Validation failed",
    "error":[]
}
```

---

# Request Validation

Validation includes:

- Required fields
- Email format
- Password strength
- Numeric ranges
- Date validation
- Duplicate resource checks

---

# Authentication Middleware

Every protected API checks:

```
Authorization Header

↓

JWT Verification

↓

Role Validation

↓

Controller Access
```

---

# API Security

Security mechanisms include:

- JWT Authentication
- Password Hashing (bcrypt)
- HTTPS
- CORS
- Helmet
- Rate Limiting
- Input Sanitization
- Request Validation

---

# Rate Limiting

Example Policy

```
100 Requests / Minute

Per IP Address
```

---

# Pagination

Example

```
GET /telemetry/history?page=2&limit=50
```

Response

```json
{
    "page":2,
    "limit":50,
    "totalPages":18,
    "totalRecords":900,
    "data":[]
}
```

---

# Filtering

Examples

```
GET /machines?status=RUNNING

GET /alerts?severity=HIGH

GET /telemetry?machineId=CNC-001
```

---

# Sorting

Examples

```
GET /telemetry?sort=timestamp

GET /machines?sort=name
```

---

# WebSocket APIs

Client Events

```
join_machine

leave_machine

subscribe_dashboard

unsubscribe_dashboard
```

Server Events

```
machine_update

telemetry_update

machine_alert

dashboard_metrics

analytics_update
```

---

# API Error Flow

```
Request

↓

Router

↓

Authentication

↓

Validation

↓

Controller

↓

Service

↓

Database

↓

Response

↓

Error Handler (if required)
```

---

# API Lifecycle

```
Client Request

↓

Middleware

↓

Controller

↓

Business Logic

↓

Database

↓

JSON Response
```

---

# Best Practices Followed

- RESTful endpoint naming
- Thin controllers
- Business logic in services
- Reusable middleware
- Centralized error handling
- Consistent JSON responses
- Secure authentication
- API versioning
- Modular routing
- Comprehensive validation

---

# Summary

The CNC Digital Twin API architecture provides a secure, scalable, and maintainable communication layer between the frontend, backend services, IoT devices, and databases. REST APIs handle CRUD operations and business workflows, while WebSocket APIs deliver low-latency telemetry and alert updates required for real-time industrial monitoring.