# 18_Codebase_Walkthrough.md

# Codebase Walkthrough

## Overview

The CNC Digital Twin project follows a modular monorepo-style structure that separates the frontend, backend, machine learning components, documentation, and project assets into independent modules. This separation improves maintainability, scalability, and ease of development.

The repository consists of five primary parts:

```
CNC_DIGITAL_TWIN-main/

├── backend/
├── frontend/
├── ml/
├── docs/
├── images/
│
├── README.md
├── LICENSE
└── .gitignore
```

---

# Repository Structure

```
CNC_DIGITAL_TWIN-main
│
├── backend
│
├── frontend
│
├── ml
│
├── docs
│
├── images
│
├── README.md
│
├── LICENSE
│
└── .gitignore
```

Each directory has a dedicated responsibility.

---

# 1. Backend Module

Location

```
backend/
```

Purpose

The backend implements the server-side logic of the application.

Responsibilities include:

- REST APIs
- Business logic
- Telemetry processing
- Database communication
- Authentication
- Real-time communication
- AI integration

Current Files

```
backend/

server.js

package.json

package-lock.json

.env.example
```

---

## server.js

This is the backend entry point.

Responsibilities

- Creates Express application
- Initializes middleware
- Registers routes
- Starts HTTP server
- Initializes Socket.IO
- Connects application modules

Execution Flow

```
server.js

↓

Express App

↓

Middleware

↓

Routes

↓

Controllers

↓

Services

↓

Database
```

---

## package.json

Defines

- Project metadata
- Dependencies
- Scripts
- Runtime requirements

Typical scripts

```
npm install

npm start

npm run dev
```

---

## .env.example

Provides template configuration.

Example

```
PORT=

MONGO_URI=

JWT_SECRET=

CLIENT_URL=
```

Actual secrets are stored in a local `.env` file.

---

# 2. Frontend Module

Location

```
frontend/
```

Purpose

Provides the React-based user interface for monitoring CNC machines.

Structure

```
frontend/

public/

src/

package.json

vite.config.ts

tsconfig.json

index.html
```

---

## public/

Contains

- favicon
- SVG assets
- Static resources

---

## src/

Contains the application source code.

Important files

```
main.tsx

App.tsx

App.css

index.css

assets/
```

---

### main.tsx

Application entry point.

Responsibilities

```
Create React Application

↓

Render App Component

↓

Attach to DOM
```

---

### App.tsx

Root React component.

Responsibilities

- Dashboard layout
- Routing
- Global UI composition

---

### assets/

Contains

```
hero.png

react.svg

vite.svg
```

---

## Vite Configuration

```
vite.config.ts
```

Responsibilities

- Build configuration
- Development server
- Module resolution
- Plugin configuration

---

# 3. Machine Learning Module

Location

```
ml/
```

Purpose

Contains AI-related functionality for anomaly detection and dataset replay.

Current Files

```
anomaly_detector.py

dataset_replay.py
```

---

## anomaly_detector.py

Purpose

Processes telemetry data to detect abnormal machine behavior.

Possible responsibilities

- Feature extraction
- Threshold analysis
- Anomaly prediction
- Model inference

Output

```
Normal

OR

Anomaly
```

---

## dataset_replay.py

Purpose

Simulates historical telemetry.

Responsibilities

- Replay sensor readings
- Testing
- Model evaluation
- Dashboard demonstrations

Flow

```
Historical Dataset

↓

Replay

↓

Backend

↓

Dashboard
```

---

# 4. Documentation Module

Location

```
docs/
```

Purpose

Contains technical documentation for the project.

Structure

```
docs/

architecture/

api/

decisions/
```

---

## architecture/

Contains architectural documentation.

Examples

```
System Architecture

Component Architecture

Data Flow

Backend Architecture

ML Pipeline

Deployment

Sequence Diagram
```

---

## api/

Documents REST APIs.

---

## decisions/

Contains Architecture Decision Records (ADRs).

Current ADR

```
ADR_Telemetry-Source.md
```

Purpose

Documents important engineering decisions.

---

# 5. Images Module

Location

```
images/
```

Purpose

Contains project screenshots.

Current image

```
dashboard.png
```

Used for

- README
- Documentation
- Demonstrations

---

# Root Files

## README.md

Project overview

Includes

- Features
- Installation
- Usage
- Architecture
- Screenshots

---

## LICENSE

Defines project licensing.

---

## .gitignore

Specifies files excluded from version control.

Examples

```
node_modules/

.env

dist/

build/
```

---

# Technology Distribution

```
Frontend

↓

React

↓

TypeScript

↓

Vite
```

---

```
Backend

↓

Node.js

↓

Express
```

---

```
Machine Learning

↓

Python
```

---

```
Documentation

↓

Markdown
```

---

# Project Execution Flow

```
Developer

↓

Frontend

↓

REST API

↓

Backend

↓

Machine Learning

↓

Database

↓

Socket.IO

↓

Frontend Dashboard
```

---

# Responsibilities by Module

| Module | Responsibility |
|----------|----------------|
| frontend | User interface |
| backend | APIs & business logic |
| ml | AI & anomaly detection |
| docs | Technical documentation |
| images | Screenshots & assets |

---

# Dependency Flow

```
Frontend

↓

Backend APIs

↓

Business Logic

↓

Machine Learning

↓

Database

↓

Frontend Updates
```

---

# Build Flow

```
Frontend

↓

Vite Build

↓

Static Assets
```

```
Backend

↓

Node.js Runtime

↓

Express Server
```

```
ML

↓

Python Runtime

↓

Prediction Service
```

---

# Development Workflow

```
Clone Repository

↓

Install Dependencies

↓

Configure .env

↓

Start Backend

↓

Start Frontend

↓

Run ML Services

↓

Open Dashboard
```

---

# Interview Talking Points

If explaining the repository:

- The project follows a modular architecture with separate frontend, backend, ML, documentation, and assets directories.
- React + TypeScript powers the frontend, while Node.js + Express handle backend services.
- The ML module is isolated, allowing AI models to evolve independently of the backend.
- Documentation is version-controlled alongside the code, improving maintainability.
- The separation of concerns makes the project easier to scale, test, and deploy.

---

# Summary

The CNC Digital Twin repository is organized into clearly defined modules, each responsible for a specific concern. The modular structure simplifies development, enables independent evolution of the frontend, backend, and ML components, and provides a strong foundation for future scalability and maintainability.