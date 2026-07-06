# 24_Testing_Strategy.md

# Testing Strategy

## Overview

The CNC Digital Twin platform consists of multiple independently running services:

- React Frontend
- Express Backend
- MongoDB
- MQTT Broker
- Python AI Service

Testing focuses on validating each component individually as well as the interactions between them.

---

# Testing Pyramid

```
                 E2E Tests
              Integration Tests
                 Unit Tests
```

---

# Unit Testing

## Frontend

Framework

```
Vitest
React Testing Library
```

Test Cases

- Component rendering
- Dashboard cards
- Charts
- Utility functions
- State updates

---

## Backend

Framework

```
Jest
Supertest
```

Test Cases

- API endpoints
- Controllers
- Services
- Validation
- Utility functions

Example

```
GET /health

Expected

200 OK
```

---

## AI Module

Framework

```
pytest
```

Test Cases

- Isolation Forest prediction
- Rule engine
- Feature extraction
- Alert generation

---

# Integration Testing

Test communication between:

```
Frontend

↓

Backend

↓

MongoDB
```

---

Also verify

```
MQTT

↓

Backend
```

---

And

```
MQTT

↓

AI

↓

MQTT Alerts

↓

Backend
```

---

# End-to-End Testing

Framework

```
Playwright
```

Scenarios

- Dashboard loads
- Telemetry appears
- Alerts generated
- Charts update
- History loads

---

# API Testing

Tools

- Postman
- Bruno
- curl

Endpoints

```
GET /health

GET /api/history
```

Verify

- Status code
- Response body
- Error handling

---

# Performance Testing

Tools

- k6
- Apache JMeter

Metrics

- API latency
- Throughput
- Concurrent users
- Memory usage

---

# AI Validation

Metrics

- Precision
- Recall
- F1 Score
- False Positives
- False Negatives

---

# Regression Testing

Performed after:

- Feature additions
- Bug fixes
- Dependency updates

---

# Manual Testing Checklist

- Backend starts
- Frontend loads
- MongoDB connects
- MQTT receives messages
- AI generates alerts
- Socket.IO updates dashboard
- History API works
- Charts render correctly

---

# CI Testing

Pipeline

```
Install

↓

Lint

↓

Unit Tests

↓

Integration Tests

↓

Build

↓

Deploy
```

---

# Summary

The project follows a layered testing strategy combining unit, integration, API, performance, AI validation, and end-to-end testing to ensure reliability across all services.