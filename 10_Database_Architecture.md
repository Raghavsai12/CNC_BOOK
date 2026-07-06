# 10_Database_Architecture.md

# Database Architecture

## Overview

The CNC Digital Twin platform uses **MongoDB** as its primary database for storing user information, CNC machine metadata, telemetry data, alerts, analytics, and system logs.

A NoSQL database was selected because industrial IoT systems generate large volumes of semi-structured telemetry data that must be ingested and queried efficiently.

The database is designed to support:

- High-frequency telemetry ingestion
- Real-time dashboard updates
- Historical analytics
- Predictive maintenance
- Horizontal scalability
- Flexible schema evolution

---

# Database Technology Stack

| Component | Technology |
|------------|------------|
| Database | MongoDB |
| ODM | Mongoose |
| Driver | MongoDB Node Driver |
| Indexing | Compound + Single Field Indexes |
| Backup | MongoDB Dump |
| Future Scaling | MongoDB Replica Set + Sharding |

---

# Database Architecture

```
                Express Backend
                       │
                 Mongoose ODM
                       │
        ┌──────────────┼──────────────┐
        │              │              │
    User Data      Machine Data   Telemetry Data
        │              │              │
        └──────────────┼──────────────┘
                       │
                  MongoDB Server
```

---

# Collections

The application consists of the following collections.

```
users

machines

telemetry

alerts

analytics

logs
```

---

# Collection Relationships

```
User
 │
 ├── owns
 │
 ▼
Machine
 │
 ├── generates
 │
 ▼
Telemetry
 │
 ├── creates
 │
 ▼
Alerts
 │
 └── feeds
      ▼
Analytics
```

---

# Users Collection

Purpose

Stores authentication and user profile information.

Example Document

```json
{
  "_id": "ObjectId",
  "name": "John Doe",
  "email": "john@example.com",
  "password": "hashed_password",
  "role": "admin",
  "createdAt": "2026-07-01",
  "updatedAt": "2026-07-01"
}
```

Fields

| Field | Type |
|--------|------|
| name | String |
| email | String |
| password | String |
| role | String |
| createdAt | Date |
| updatedAt | Date |

Indexes

```
email (unique)
```

---

# Machines Collection

Purpose

Stores CNC machine metadata.

Example Document

```json
{
  "_id": "ObjectId",
  "machineId": "CNC-001",
  "name": "Vertical Milling Machine",
  "status": "RUNNING",
  "location": "Factory A",
  "manufacturer": "Mazak",
  "model": "VCN-530C",
  "installedAt": "2025-05-10"
}
```

Fields

| Field | Type |
|--------|------|
| machineId | String |
| name | String |
| manufacturer | String |
| model | String |
| status | String |
| location | String |
| installedAt | Date |

Indexes

```
machineId (unique)

status

location
```

---

# Telemetry Collection

Purpose

Stores high-frequency sensor readings.

Example Document

```json
{
  "_id": "ObjectId",
  "machineId": "CNC-001",
  "rpm": 3500,
  "temperature": 68.5,
  "vibration": 0.02,
  "power": 14.8,
  "toolWear": 23,
  "timestamp": "2026-07-05T10:20:00Z"
}
```

Fields

| Field | Type |
|--------|------|
| machineId | String |
| rpm | Number |
| temperature | Number |
| vibration | Number |
| power | Number |
| toolWear | Number |
| timestamp | Date |

Indexes

```
machineId

timestamp

machineId + timestamp (compound)
```

---

# Alerts Collection

Purpose

Stores generated alerts.

Example Document

```json
{
  "_id": "ObjectId",
  "machineId": "CNC-001",
  "type": "Temperature",
  "severity": "HIGH",
  "message": "Temperature exceeded threshold",
  "status": "OPEN",
  "createdAt": "2026-07-05"
}
```

Fields

| Field | Type |
|--------|------|
| machineId | String |
| type | String |
| severity | String |
| message | String |
| status | String |
| createdAt | Date |

Indexes

```
machineId

severity

status
```

---

# Analytics Collection

Purpose

Stores aggregated metrics generated periodically.

Example Document

```json
{
  "_id": "ObjectId",
  "machineId": "CNC-001",
  "oee": 92.5,
  "availability": 95,
  "performance": 94,
  "quality": 98,
  "generatedAt": "2026-07-05"
}
```

Fields

| Field | Type |
|--------|------|
| machineId | String |
| oee | Number |
| availability | Number |
| performance | Number |
| quality | Number |
| generatedAt | Date |

Indexes

```
machineId

generatedAt
```

---

# Logs Collection

Purpose

Stores system logs and audit events.

Example Document

```json
{
  "_id": "ObjectId",
  "level": "ERROR",
  "service": "Telemetry Service",
  "message": "Database timeout",
  "timestamp": "2026-07-05T14:30:00Z"
}
```

Fields

| Field | Type |
|--------|------|
| level | String |
| service | String |
| message | String |
| timestamp | Date |

Indexes

```
level

timestamp
```

---

# Entity Relationship Overview

```
Users
  │
  └──────────────┐
                 │
             Machines
                 │
      ┌──────────┴──────────┐
      │                     │
Telemetry             Alerts
      │                     │
      └──────────┬──────────┘
                 │
            Analytics
```

---

# Data Lifecycle

### Machine Registration

```
Machine Added

↓

Machine Collection
```

---

### Telemetry Flow

```
Sensor Reading

↓

Backend API

↓

Validation

↓

Telemetry Collection

↓

Dashboard

↓

Analytics Engine

↓

Alerts
```

---

# Data Retention Strategy

| Data | Retention |
|--------|-----------|
| Telemetry | 12 Months |
| Alerts | 2 Years |
| Analytics | Permanent |
| Logs | 90 Days |

Future enhancements:

- Automatic archival to cold storage
- Time-series collections
- Data compression

---

# Backup Strategy

Daily

- Incremental backup

Weekly

- Full backup

Monthly

- Offsite backup

Disaster Recovery

- Replica set failover
- Automated restore testing

---

# Performance Optimizations

- Compound indexes for telemetry queries
- Bulk insert operations
- Pagination for historical data
- Aggregation pipelines
- Read/write separation (future)
- Connection pooling

---

# Security

- Authentication before database access
- Role-based authorization
- Encrypted database connections (TLS)
- Password hashing using bcrypt
- Input validation
- Audit logging

---

# Scalability Roadmap

Current

```
Single MongoDB Instance
```

Phase 2

```
Replica Set
```

Phase 3

```
Replica Set

↓

Sharding

↓

Distributed Cluster
```

---

# Summary

The database architecture is optimized for industrial IoT workloads where telemetry is generated continuously. MongoDB's document-oriented model provides flexibility for evolving machine schemas, while indexing and aggregation pipelines ensure efficient querying for dashboards, analytics, and predictive maintenance.