# 17_AI_Analytics_Architecture.md

# AI & Analytics Architecture

## Overview

The CNC Digital Twin platform combines real-time telemetry with analytics to provide operational insights, detect anomalies, and enable predictive maintenance. The analytics pipeline transforms raw sensor readings into meaningful business intelligence that helps operators improve machine utilization, reduce downtime, and optimize production.

Although the current implementation focuses on rule-based analytics, the architecture is designed to support machine learning models and AI services in future versions.

---

# Objectives

The AI & Analytics layer is responsible for:

- Processing telemetry data
- Calculating KPIs
- Detecting abnormal machine behavior
- Generating maintenance recommendations
- Predicting equipment failures
- Supporting historical analysis
- Improving operational efficiency

---

# High-Level Architecture

```
                  CNC Machine
                       │
                  Telemetry Data
                       │
                       ▼
              Telemetry Service
                       │
                       ▼
                 MongoDB Storage
                       │
         ┌─────────────┴─────────────┐
         │                           │
         ▼                           ▼
 Rule-Based Analytics         Historical Analysis
         │                           │
         └─────────────┬─────────────┘
                       ▼
              Analytics Service
                       │
                       ▼
                REST / Socket.IO
                       │
                       ▼
               Frontend Dashboard
```

---

# Analytics Pipeline

```
Telemetry Received

↓

Validate Data

↓

Store Database

↓

Calculate Metrics

↓

Threshold Evaluation

↓

Generate Alerts

↓

Update Dashboard

↓

Store Analytics
```

---

# Data Sources

The analytics engine processes information from multiple sources.

| Source | Description |
|----------|-------------|
| RPM | Spindle speed |
| Temperature | Machine temperature |
| Vibration | Mechanical vibration |
| Power | Energy consumption |
| Tool Wear | Tool degradation |
| Machine Status | Running / Idle / Fault |
| Historical Telemetry | Previous sensor readings |

---

# Analytics Modules

## 1. Operational Analytics

Provides real-time operational metrics.

Examples:

- Current RPM
- Temperature
- Machine uptime
- Active machines
- Running status

---

## 2. Performance Analytics

Measures production efficiency.

Metrics include:

- OEE
- Availability
- Performance
- Quality
- Production rate

---

## 3. Energy Analytics

Calculates:

- Power consumption
- Peak usage
- Average usage
- Daily energy usage
- Energy efficiency

---

## 4. Maintenance Analytics

Monitors:

- Tool wear
- Machine temperature
- Bearing vibration
- Runtime hours
- Maintenance schedule

---

# KPI Calculation

Example KPIs

```
Machine Availability

Machine Utilization

Average RPM

Average Temperature

Downtime

Fault Count

Energy Consumption

OEE
```

---

# OEE Calculation

Overall Equipment Effectiveness

```
OEE

=

Availability

×

Performance

×

Quality
```

Example

```
Availability = 95%

Performance = 92%

Quality = 98%

↓

OEE = 85.7%
```

---

# Alert Generation

Rule-based alerts are generated when thresholds are exceeded.

Example

```
Temperature > 80°C

↓

High Temperature Alert
```

Other examples:

```
RPM exceeds limit

↓

Overspeed Alert
```

```
Vibration exceeds threshold

↓

Bearing Warning
```

---

# Historical Analytics

Historical telemetry enables:

- Trend analysis
- Daily reports
- Weekly reports
- Monthly reports
- Performance comparison

---

# Dashboard Metrics

Displayed metrics include:

- Machine Count
- Active Machines
- Offline Machines
- Average Temperature
- Active Alerts
- Energy Usage
- Downtime
- OEE

---

# Time-Series Analysis

Telemetry is analyzed over time to identify:

- Increasing temperature trends
- Vibration growth
- Energy usage spikes
- Machine degradation

Example

```
Time

↓

Temperature

↓

Trend

↓

Potential Failure
```

---

# Predictive Maintenance (Future)

Future machine learning models may predict failures using:

Inputs

- Temperature
- RPM
- Vibration
- Tool Wear
- Runtime
- Historical failures

Outputs

- Failure probability
- Remaining useful life (RUL)
- Recommended maintenance date

---

# Possible ML Algorithms

Potential models include:

- Random Forest
- XGBoost
- Isolation Forest
- LSTM
- Autoencoders
- ARIMA
- Prophet

The architecture is model-agnostic, allowing different algorithms to be integrated as requirements evolve.

---

# AI Workflow (Future)

```
Historical Data

↓

Feature Engineering

↓

Model Training

↓

Model Validation

↓

Model Deployment

↓

Prediction API

↓

Dashboard
```

---

# Analytics Data Flow

```
Telemetry

↓

MongoDB

↓

Analytics Service

↓

Metric Calculation

↓

REST API

↓

Frontend Charts
```

---

# Real-Time Analytics

The analytics service continuously processes incoming telemetry.

Flow

```
Sensor Reading

↓

Analytics Engine

↓

Calculate Metrics

↓

Emit Dashboard Update
```

---

# Data Aggregation

Aggregation intervals:

| Interval | Purpose |
|-----------|----------|
| 1 Minute | Live monitoring |
| 5 Minutes | Dashboard summaries |
| 1 Hour | Operational reports |
| 1 Day | Historical reports |

---

# Performance Considerations

To maintain responsiveness:

- Precompute common metrics
- Cache dashboard summaries
- Use aggregation pipelines
- Index telemetry data
- Batch updates
- Minimize recalculations

---

# Future AI Enhancements

Potential capabilities include:

- Predictive maintenance
- Remaining Useful Life estimation
- Failure classification
- Root cause analysis
- AI-generated maintenance recommendations
- Natural language dashboard assistant
- Anomaly detection using ML
- Demand forecasting
- Production optimization

---

# Integration with Frontend

Analytics are delivered through:

REST APIs

```
GET /analytics/dashboard

GET /analytics/oee

GET /analytics/trends
```

Socket.IO Events

```
analytics_update

dashboard_update

alert_created
```

---

# Interview Talking Points

When discussing analytics:

- Explain the difference between descriptive, diagnostic, predictive, and prescriptive analytics.
- Describe how telemetry is converted into KPIs.
- Explain why historical data is important for trend analysis.
- Discuss how rule-based alerts work today and how ML models could replace or augment them.
- Explain why the architecture separates telemetry ingestion from analytics processing.

---

# Summary

The AI & Analytics architecture transforms raw CNC telemetry into actionable operational insights. The current implementation provides real-time KPI calculation, historical reporting, and rule-based alerting, while the modular design allows future integration of advanced machine learning models for predictive maintenance, anomaly detection, and intelligent decision support.