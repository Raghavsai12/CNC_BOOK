# 22_Project_FAQs.md

# CNC Digital Twin Project FAQs

## Overview

This document contains concise, interview-ready answers to frequently asked technical questions about the CNC Digital Twin project. The questions cover architecture, frontend, backend, databases, IoT, AI/ML, deployment, scalability, and software engineering concepts.

---

# Project Basics

### Q1. What is the CNC Digital Twin project?

A real-time Industrial IoT monitoring platform that simulates CNC laser machine telemetry, detects anomalies using machine learning, stores historical data, and visualizes machine health through a React dashboard.

---

### Q2. What problem does it solve?

It enables real-time monitoring, early anomaly detection, historical analysis, and operational insights, reducing machine downtime and improving maintenance planning.

---

### Q3. What is a Digital Twin?

A Digital Twin is a virtual representation of a physical asset that continuously receives live operational data to mirror the current state of the physical system.

---

### Q4. Why is it called a Digital Twin?

Because the dashboard continuously reflects the machine's operating conditions using live telemetry data.

---

# Architecture

### Q5. Which architecture pattern does the project follow?

Event-driven, modular, and loosely coupled architecture.

---

### Q6. What are the major modules?

- React Frontend
- Express Backend
- MQTT Broker
- Python AI Service
- MongoDB
- Socket.IO

---

### Q7. Why modular architecture?

To improve maintainability, scalability, independent deployment, and easier testing.

---

### Q8. How do modules communicate?

Using MQTT, REST APIs, and Socket.IO.

---

### Q9. Which component orchestrates the system?

The Node.js backend orchestrates APIs, MQTT subscriptions, Socket.IO communication, and database persistence.

---

### Q10. Is the AI tightly coupled with the backend?

No. The AI runs as an independent service and communicates through MQTT.

---

# Frontend

### Q11. Which frontend framework is used?

React with TypeScript.

---

### Q12. Why React?

Because it provides component-based architecture, efficient rendering through the Virtual DOM, and a large ecosystem.

---

### Q13. Why Vite instead of Create React App?

Vite offers significantly faster startup time, hot module replacement (HMR), and optimized builds.

---

### Q14. How does the dashboard receive updates?

Through Socket.IO.

---

### Q15. Does the dashboard use polling?

No.

It receives push-based updates over WebSockets.

---

### Q16. Why use WebSockets?

To reduce latency and eliminate unnecessary HTTP polling.

---

### Q17. How is historical data loaded?

Using REST APIs.

---

### Q18. Why use REST and WebSockets together?

REST is used for initial data retrieval, while WebSockets deliver continuous real-time updates.

---

# Backend

### Q19. Which backend framework is used?

Express.js.

---

### Q20. Why Express?

Lightweight, flexible, middleware support, and excellent integration with Socket.IO.

---

### Q21. What does the backend do?

- Receives telemetry
- Stores data
- Hosts REST APIs
- Runs Socket.IO
- Forwards AI alerts
- Connects MongoDB

---

### Q22. Does the backend run machine learning?

No.

Machine learning runs in a separate Python service.

---

### Q23. Why separate the AI service?

Independent scaling, deployment, maintenance, and language flexibility.

---

# MQTT

### Q24. What is MQTT?

A lightweight publish-subscribe messaging protocol designed for IoT.

---

### Q25. Why MQTT?

Low bandwidth, asynchronous communication, and loose coupling.

---

### Q26. Which MQTT broker is used?

HiveMQ public broker.

---

### Q27. What topics are used?

```
griet/cnc/telemetry

griet/cnc/alerts
```

---

### Q28. Who publishes telemetry?

`dataset_replay.py`

---

### Q29. Who subscribes to telemetry?

- Backend
- AI Watchdog

---

### Q30. Why publish-subscribe instead of HTTP?

Multiple consumers can independently process telemetry without modifying the publisher.

---

# Database

### Q31. Which database is used?

MongoDB Atlas.

---

### Q32. Why MongoDB?

Flexible schema, JSON documents, high write throughput, and time-series support.

---

### Q33. What data is stored?

- Sensor readings
- Timestamps
- Machine state
- Anomaly flags

---

### Q34. Why store historical telemetry?

Trend analysis, dashboards, reporting, and future ML model training.

---

# AI / ML

### Q35. Which ML algorithm is used?

Isolation Forest.

---

### Q36. Why Isolation Forest?

It performs unsupervised anomaly detection without requiring labeled datasets.

---

### Q37. Is only ML used?

No.

The project combines rule-based logic with machine learning.

---

### Q38. Why hybrid detection?

Rule-based checks quickly catch known faults, while ML detects unknown or unusual patterns.

---

### Q39. What happens after anomaly detection?

The AI service publishes an MQTT alert, which is displayed on the dashboard.

---

### Q40. Does the ML model retrain automatically?

No.

The current implementation performs inference only.

---

# Real-Time Communication

### Q41. Why Socket.IO?

Reliable real-time communication with automatic reconnection.

---

### Q42. What events are emitted?

Examples include:

- `telemetry_stream`
- `ml_alert`

---

### Q43. How fast does the dashboard update?

Nearly instantly after receiving backend broadcasts.

---

# Deployment

### Q44. Can this project run locally?

Yes.

Frontend, backend, MongoDB, and Python services can run on a developer machine.

---

### Q45. Is it Docker-ready?

Yes.

The architecture supports containerization.

---

### Q46. How would you deploy it?

- Docker
- Nginx
- Cloud VM or Kubernetes
- MongoDB Atlas
- CI/CD pipeline

---

# Scalability

### Q47. How would you support thousands of machines?

- Multiple backend instances
- Load balancer
- Redis adapter for Socket.IO
- Kafka
- MongoDB replica sets

---

### Q48. Can multiple AI workers run simultaneously?

Yes.

Because MQTT allows multiple subscribers.

---

# Security

### Q49. How are APIs protected?

JWT authentication and role-based authorization.

---

### Q50. How are passwords stored?

Using bcrypt hashing.

---

### Q51. Why use HTTPS?

To encrypt communication and prevent man-in-the-middle attacks.

---

# Software Engineering

### Q52. Which design principles are demonstrated?

- Separation of concerns
- Single responsibility
- Loose coupling
- Event-driven design
- Modular architecture

---

### Q53. Which communication styles are used?

- REST
- MQTT
- WebSockets

---

### Q54. Why event-driven architecture?

Because telemetry arrives continuously and multiple services must react independently.

---

### Q55. Why not make the AI service a backend module?

Keeping it independent simplifies scaling, experimentation, and maintenance.

---

# Resume Questions

### Q56. What was your primary contribution?

Designing and integrating the frontend, backend, telemetry processing, AI pipeline, database persistence, and real-time communication.

---

### Q57. What was the biggest technical challenge?

Coordinating asynchronous communication between MQTT, Socket.IO, MongoDB, and the AI service while maintaining real-time responsiveness.

---

### Q58. What would you improve next?

- Predictive maintenance
- User authentication
- Kubernetes deployment
- MQTT security
- Better visualization
- Multi-machine support

---

### Q59. What did you learn?

- Event-driven system design
- Real-time communication
- Industrial IoT concepts
- AI integration
- Backend architecture
- Distributed systems

---

### Q60. Why is this project valuable?

It combines frontend, backend, databases, messaging systems, AI/ML, and real-time processing into one production-style application, demonstrating end-to-end software engineering skills.

---

# Rapid Fire Interview Questions

Be prepared to answer these in one or two sentences:

- What is MQTT?
- What is Socket.IO?
- Why MongoDB?
- Why Isolation Forest?
- What is a Digital Twin?
- Explain publish-subscribe.
- Difference between REST and WebSockets.
- How does telemetry reach the dashboard?
- Why separate AI into another service?
- How would you scale the application?
- How would you secure MQTT?
- What happens if MongoDB goes down?
- What happens if the AI service fails?
- How would you deploy to Kubernetes?
- Why event-driven architecture?

---

# Final Interview Tips

- Begin with the business problem before discussing technologies.
- Explain the complete telemetry lifecycle from generation to visualization.
- Highlight design decisions and trade-offs, not just technologies.
- Be honest about current limitations and discuss future improvements.
- Use diagrams from the architecture documents to support explanations.

---

# Summary

This FAQ provides concise, interview-ready answers covering all major aspects of the CNC Digital Twin project. Reviewing these questions regularly will help build confidence in explaining the project's architecture, implementation, and engineering decisions during technical interviews.