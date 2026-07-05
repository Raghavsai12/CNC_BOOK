# Chapter 01 — Project Overview

# CNC Digital Twin

## Executive Summary

The CNC Digital Twin project is a full-stack Industrial Internet of Things (IIoT) application designed to simulate the real-time monitoring of a CNC laser cutting machine. The system demonstrates how industrial telemetry can be collected, transmitted, analyzed, stored, and visualized while incorporating machine learning to identify abnormal operating conditions.

Unlike a traditional dashboard project that simply visualizes static data, this project models a complete industrial telemetry pipeline consisting of data generation, message brokering, backend processing, database persistence, anomaly detection, alert generation, and real-time visualization.

The project was initially developed using physical IoT hardware (ESP32 with industrial sensors) to validate telemetry transmission over MQTT. As the project evolved, the telemetry source was replaced by an Industrial Telemetry Simulator that replays representative machine data derived from observations of an actual CNC laser cutting machine. This allowed the complete software architecture to be developed, tested, and demonstrated without requiring continuous access to industrial hardware.

The final system demonstrates concepts commonly found in modern Industry 4.0 manufacturing environments, including event-driven communication, distributed system design, predictive maintenance, and hybrid anomaly detection.

---

# 1. Background

Modern manufacturing machines generate thousands of telemetry points every minute.

Typical telemetry includes:

- Temperature
- Vibration
- Acceleration
- Pressure
- Motor current
- Tool position
- Feed rate
- Spindle speed
- Gas pressure
- Machine state

Monitoring this information allows engineers to detect abnormal machine behavior before catastrophic failures occur.

Industries increasingly rely on predictive maintenance systems instead of reactive maintenance.

Instead of repairing machines after failure, predictive maintenance attempts to identify abnormal behavior before breakdowns occur.

This project demonstrates a simplified implementation of that concept.

---

# 2. Why This Project Exists

This project was created to understand and demonstrate the complete software pipeline involved in industrial telemetry monitoring.

The objective was not simply to build a dashboard.

Instead, the goal was to explore how data moves through an industrial monitoring system from the moment telemetry is generated until actionable alerts are presented to an operator.

The project also served as an opportunity to gain practical experience with:

- MQTT messaging
- Event-driven backend systems
- Industrial telemetry processing
- Machine learning–based anomaly detection
- Real-time frontend development
- Cloud deployment
- Distributed application architecture

---

# 3. Project Evolution

The project evolved through multiple stages.

## Phase 1 — Hardware Prototype

The initial implementation used:

- ESP32
- ADXL345 Accelerometer
- DS18B20 Temperature Sensor

The objective was to validate:

- Sensor acquisition
- MQTT publishing
- Backend connectivity
- Real-time dashboard updates

This phase demonstrated that live telemetry could be collected and transmitted successfully.

---

## Phase 2 — Industrial Research

To make the project more representative of real manufacturing environments, observations were gathered from a CNC laser cutting machine through discussions with an industry contact.

This helped identify:

- Typical operating parameters
- Machine states
- Expected vibration behavior
- Temperature ranges
- Gas pressure characteristics
- Fault scenarios

These observations informed the design of the simulated telemetry dataset.

---

## Phase 3 — Industrial Telemetry Simulator

Continuous access to physical hardware was impractical.

Instead of depending on the ESP32 for every demonstration, the project transitioned to a replay-based telemetry simulator.

The simulator streams representative telemetry records from a CSV dataset over MQTT at a controlled rate, emulating the behavior of a live machine.

This approach provides:

- Repeatable demonstrations
- Deterministic testing
- Easier debugging
- Faster development
- Hardware-independent deployment

---

## Phase 4 — Hybrid AI Monitoring

The final enhancement introduced a Hybrid AI Watchdog.

Rather than relying solely on machine learning, anomaly detection combines:

- Rule-based industrial thresholds
- Isolation Forest anomaly detection

This hybrid approach reduces false positives while ensuring critical faults are always detected.

---

# 4. Problem Statement

Manufacturing machines operate continuously under varying workloads.

Unexpected failures can result in:

- Production downtime
- Equipment damage
- Increased maintenance costs
- Reduced productivity
- Safety risks

Traditional monitoring systems often rely only on threshold alarms.

These systems cannot identify subtle changes that indicate developing failures.

Machine learning can identify these patterns but may also generate false positives if used alone.

This project explores how deterministic engineering rules and machine learning can work together to improve anomaly detection.

---

# 5. Project Goals

Primary goals included:

- Build a complete IIoT telemetry pipeline.
- Learn MQTT communication.
- Design an event-driven backend.
- Store telemetry in MongoDB.
- Implement hybrid anomaly detection.
- Create a responsive real-time dashboard.
- Deploy the application to the cloud.
- Simulate realistic industrial telemetry.
- Produce a portfolio-quality software engineering project.

---

# 6. Intended Users

The system is intended for demonstration purposes.

Representative users include:

Industrial Operators

Monitor live machine health.

Maintenance Engineers

Investigate abnormal operating conditions.

Plant Supervisors

Observe production trends.

Software Engineers

Study distributed IIoT architecture.

Students

Learn MQTT, Node.js, React, Python, and machine learning.

Recruiters

Evaluate software engineering capabilities.

---

# 7. Business Value

Although this is a portfolio project, it models business problems commonly encountered in manufacturing.

Potential benefits include:

Reduced machine downtime

Earlier fault detection

Improved maintenance planning

Better operator awareness

Historical machine analytics

Scalable telemetry architecture

---

# 8. Technical Value

This project demonstrates knowledge across multiple engineering domains.

Backend Engineering

- MQTT
- Express
- Socket.IO
- REST APIs
- Event-driven programming

Frontend Engineering

- React
- TypeScript
- Real-time visualization
- State management

Machine Learning

- Isolation Forest
- Feature engineering
- Hybrid anomaly detection

Cloud

- Vercel
- Render
- MongoDB Atlas

Industrial IoT

- MQTT messaging
- Telemetry processing
- Digital Twin concepts

---

# 9. Scope

Included

✓ Real-time telemetry

✓ MQTT messaging

✓ Cloud deployment

✓ MongoDB persistence

✓ AI anomaly detection

✓ Dashboard visualization

✓ Historical charts

✓ Alert generation

Not Included

✗ User authentication

✗ Role-based authorization

✗ Docker

✗ CI/CD

✗ Kubernetes

✗ Production monitoring

✗ Automated testing

✗ Multi-machine support

These are identified as future improvements.

---

# 10. Assumptions

The project assumes:

- Telemetry values are representative of a CNC laser machine.
- MQTT broker availability.
- Stable network connectivity.
- Time synchronization across services.
- Properly formatted telemetry messages.
- Single-machine deployment.

---

# 11. Limitations

Current limitations include:

- Replay-based simulation instead of live industrial hardware.
- Single MQTT broker.
- No authentication.
- No encrypted MQTT communication.
- No persistent ML model.
- Model retrains using an in-memory rolling buffer.
- Limited fault categories.
- No horizontal scaling.
- Simplified industrial assumptions.

These limitations are acceptable for a portfolio demonstration but would require additional engineering for production deployment.

---

# 12. Success Criteria

The project is considered successful if it can:

- Stream telemetry continuously.
- Store telemetry successfully.
- Display live dashboard updates.
- Detect abnormal operating conditions.
- Publish alerts.
- Operate without physical hardware.
- Demonstrate modern software engineering practices.
- Serve as a strong portfolio project for software engineering and AI-focused roles.

---

# Chapter Summary

The CNC Digital Twin project demonstrates how a modern Industrial IoT monitoring platform can be designed using an event-driven architecture that combines real-time telemetry processing, cloud services, and machine learning.

The project evolved from a hardware prototype into a software-focused Industrial Telemetry Simulator, enabling repeatable demonstrations while preserving the architectural concepts used in real industrial monitoring systems.

Subsequent chapters will examine each subsystem in detail, beginning with the repository structure and implementation.