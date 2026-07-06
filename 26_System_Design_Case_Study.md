# 26_System_Design_Case_Study.md

# System Design Case Study

## Problem Statement

How would the CNC Digital Twin platform be redesigned to support:

- 100,000+ CNC machines
- Millions of telemetry events per hour
- Thousands of concurrent users

---

# Current Architecture

```
React

↓

Express

↓

MongoDB
```

Suitable for:

```
10–100 machines
```

---

# Production Architecture

```
               Internet

                    │

          Global Load Balancer

                    │

          API Gateway / Nginx

                    │

      Kubernetes Cluster

        │         │         │

Backend-1 Backend-2 Backend-3

        │         │

        Redis Adapter

               │

        Kafka Cluster

               │

 AI Workers

 Analytics Workers

 Alert Workers

               │

MongoDB Replica Set

               │

 Object Storage

               │

 Grafana

 Prometheus

 ELK Stack
```

---

# Why Kafka?

MQTT works well for IoT devices.

Kafka is introduced for

- durable event storage
- replay
- high throughput
- stream processing

---

# Why Kubernetes?

Benefits

- Auto-scaling
- Self-healing
- Rolling updates
- Service discovery

---

# Redis

Responsibilities

- Cache
- Session storage
- Socket.IO adapter

---

# Monitoring

Tools

- Prometheus
- Grafana
- Loki
- ELK

---

# AI Pipeline

```
Telemetry

↓

Kafka

↓

Feature Store

↓

Model Server

↓

Prediction

↓

Alert Queue
```

---

# Security

- OAuth2
- JWT
- RBAC
- TLS
- API Gateway
- Rate Limiting

---

# Disaster Recovery

- Multi-region deployment
- Automated backups
- Replica sets
- Failover
- Infrastructure as Code

---

# Cost Optimization

- Auto scaling
- Spot instances
- Cold storage
- Tiered caching

---

# Interview Discussion

If asked:

**"How would you scale your project to 100,000 CNC machines?"**

Discuss:

- Kubernetes
- Kafka
- Redis
- Load balancing
- MongoDB sharding
- Independent AI workers
- Horizontal backend scaling
- Monitoring
- CI/CD

---

# Summary

The production architecture transforms the project into a cloud-native Industrial IoT platform capable of supporting enterprise-scale telemetry processing while maintaining low latency, high availability, fault tolerance, and independent scalability of every service.