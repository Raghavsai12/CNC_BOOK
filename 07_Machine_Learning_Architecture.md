# Chapter 07 — Machine Learning & Hybrid AI Architecture

# Hybrid AI Watchdog

---

# Executive Summary

The Machine Learning subsystem is responsible for continuously monitoring industrial telemetry and identifying abnormal machine behavior.

Unlike many AI applications that rely solely on machine learning models, this project implements a **Hybrid AI Architecture**, combining deterministic industrial safety rules with unsupervised anomaly detection.

This design ensures that critical failures are detected immediately while still allowing the system to identify subtle operational deviations that fixed thresholds may miss.

The AI subsystem operates as an independent microservice that subscribes to the MQTT telemetry stream, evaluates each incoming record, and publishes alerts back to the messaging layer when anomalies are detected.

---

# 1. Purpose

The primary objective of the Hybrid AI Watchdog is to detect machine abnormalities before they become critical failures.

The system continuously analyzes incoming telemetry and determines whether the machine is operating normally or exhibiting suspicious behavior.

Unlike the backend, the AI service does not store data or serve APIs.

Its only responsibility is machine health analysis.

---

# 2. Why a Separate AI Service?

The AI pipeline was intentionally separated from the backend.

Instead of embedding machine learning logic inside the Node.js application, the anomaly detector runs as an independent Python process.

Advantages:

- Independent deployment
- Easier model experimentation
- Better fault isolation
- Easier scaling
- Language independence
- Cleaner architecture

This follows the microservice philosophy where each service owns a single responsibility.

---

# 3. High-Level AI Pipeline

```
Telemetry

↓

MQTT Subscriber

↓

Rolling Memory Buffer

↓

Rule-Based Engine

↓

Isolation Forest

↓

Decision Engine

↓

MQTT Alert Publisher
```

---

# 4. Input Data

The AI service subscribes to:

```
griet/cnc/telemetry
```

Each telemetry record contains representative machine parameters such as:

- Temperature
- X-axis acceleration
- Y-axis acceleration
- Assist gas pressure
- Machine status
- Machine serial number
- Timestamp

Each record is processed independently while also contributing to a rolling history used for machine learning.

---

# 5. Rolling Buffer

The system maintains an in-memory rolling buffer.

```python
deque(maxlen=100)
```

Purpose:

- Store recent telemetry
- Train Isolation Forest
- Maintain temporal context
- Avoid storing unlimited data

When the buffer becomes full, the oldest records are automatically removed.

Advantages:

- Constant memory usage
- Fast insertion
- No manual cleanup

---

# 6. Why a Rolling Buffer?

Isolation Forest requires historical observations to identify outliers.

Training on only the current sample would be meaningless.

The rolling buffer provides recent operational history while keeping memory usage constant.

This makes the AI service lightweight and suitable for continuous execution.

---

# 7. Rule-Based Detection

Before invoking machine learning, every telemetry record is evaluated against deterministic engineering rules.

Examples include:

Temperature

- Warning threshold
- Critical threshold

Acceleration

- Sudden violent movement
- Possible collision
- Mechanical shock

Assist Gas Pressure

- Under-pressure
- Over-pressure

These rules immediately classify known unsafe operating conditions.

---

# Why Rules First?

Critical industrial failures should never depend solely on a statistical model.

For example:

```
Temperature > 50°C
```

does not require AI.

It is always unsafe.

Using deterministic rules ensures:

- Fast detection
- Predictable behavior
- Explainable decisions

---

# 8. Machine Learning Layer

If no deterministic rule is violated, telemetry proceeds to the ML stage.

The project uses:

```
Isolation Forest
```

from Scikit-Learn.

Isolation Forest is an unsupervised anomaly detection algorithm.

It does not require labeled training data.

Instead, it learns the structure of normal operating behavior and isolates observations that appear statistically different.

---

# Why Isolation Forest?

Reasons:

- No labeled anomalies required
- Lightweight
- Fast training
- Fast inference
- Suitable for industrial telemetry
- Handles multidimensional data

---

# Features Used

The model is trained using:

- Temperature
- X-axis acceleration
- Y-axis acceleration
- Assist gas pressure

Each observation becomes a four-dimensional feature vector.

---

# Model Training

The model is retrained using the current rolling buffer.

Workflow:

```
Rolling Buffer

↓

Pandas DataFrame

↓

Feature Selection

↓

Isolation Forest

↓

Prediction
```

---

# Prediction

Isolation Forest returns:

```
1
```

Normal

or

```
-1
```

Anomaly

The project also evaluates the anomaly score using:

```
decision_function()
```

Only sufficiently abnormal observations generate alerts.

---

# Decision Engine

Final anomaly detection combines:

```
Rule Engine

+

Isolation Forest

↓

Decision
```

Possible outcomes:

Normal

Warning

Critical

AI Detected Deviation

This hybrid approach minimizes false positives while ensuring dangerous conditions are never ignored.

---

# 9. Alert Generation

When an anomaly is detected, an alert object is created.

Typical fields include:

- Machine ID
- Alert Type
- Reason
- Score
- Timestamp

The alert is serialized as JSON and published to:

```
griet/cnc/alerts
```

The backend subscribes to this topic and forwards alerts to the dashboard.

---

# 10. Why MQTT for Alerts?

Using MQTT instead of direct HTTP communication provides:

- Loose coupling
- Independent services
- Asynchronous messaging
- Easy scalability
- Multiple future subscribers

Future subscribers could include:

- Email Service
- SMS Gateway
- Grafana
- PagerDuty
- Maintenance System

without modifying the AI service.

---

# 11. Error Handling

The AI service is designed to continue running even if malformed messages are received.

If telemetry parsing fails:

- Exception is caught
- Message is ignored
- Service continues processing future telemetry

This prevents a single invalid payload from stopping anomaly detection.

---

# 12. Performance Characteristics

Memory Complexity:

O(n)

where:

n = rolling buffer size

Current implementation:

100 telemetry records

Inference Time:

Very small

Suitable for near real-time monitoring.

---

# 13. Strengths

✓ Lightweight

✓ Explainable

✓ Hybrid detection

✓ Easy to extend

✓ Independent deployment

✓ Uses unsupervised learning

✓ Constant memory

---

# 14. Current Limitations

The current implementation intentionally simplifies several production concerns.

Limitations include:

- Model retrains continuously
- No persisted model
- No feature scaling
- No hyperparameter optimization
- No concept drift detection
- Single machine support
- Limited feature set
- No confidence calibration

These choices were made to keep the project focused on demonstrating architecture rather than production-grade ML infrastructure.

---

# 15. Possible Improvements

Future enhancements include:

- Model persistence
- Incremental learning
- AutoML experimentation
- LSTM time-series forecasting
- Autoencoder anomaly detection
- Feature normalization
- Online learning
- Explainable AI (SHAP)
- Drift detection
- Model versioning
- MLflow integration

---

# 16. Why This Design Was Chosen

A purely rule-based system cannot identify subtle degradation.

A purely ML-based system may generate false alarms.

The hybrid architecture combines the strengths of both approaches.

Rule-based detection ensures deterministic safety.

Isolation Forest identifies previously unseen behavioral changes.

This mirrors approaches commonly used in industrial predictive maintenance systems.

---

# 17. Interview Talking Points

Be prepared to explain:

- Why Isolation Forest?
- Why unsupervised learning?
- Why not Random Forest?
- Why not Neural Networks?
- Why retrain on a rolling buffer?
- Why use MQTT?
- Why separate AI into its own service?
- Why hybrid detection?
- How would you scale this service?
- How would you improve model accuracy?

---

# Chapter Summary

The Hybrid AI Watchdog is responsible for continuously evaluating machine telemetry using both deterministic engineering rules and unsupervised machine learning.

By combining threshold-based safety checks with Isolation Forest anomaly detection, the system provides a balanced approach that is both explainable and adaptable.

Its independent microservice architecture allows it to evolve separately from the backend while remaining loosely coupled through MQTT messaging.