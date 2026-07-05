# 08_Frontend_Architecture.md

## Overview

The Frontend layer of the CNC Digital Twin system is responsible for providing a real-time, interactive, and responsive user interface for monitoring CNC machines, visualizing telemetry data, and controlling simulation/digital twin operations.

It is designed as a **modern SPA (Single Page Application)** with a component-driven architecture, optimized for real-time updates and industrial dashboards.

---

## 1. Frontend Architecture Style

### Chosen Architecture
- Component-Based Architecture
- State-Driven UI Architecture
- Event-Driven Updates (WebSocket + REST hybrid)
- Micro-frontend ready (optional future scaling)

---

## 2. Technology Stack

| Layer | Technology |
|------|------------|
| UI Framework | React.js (Vite-based setup) |
| Styling | TailwindCSS + CSS Modules |
| State Management | Redux Toolkit / Zustand |
| Real-time Communication | WebSockets (Socket.IO client) |
| API Communication | Axios (REST APIs) |
| Charts & Visualization | Recharts / Chart.js |
| 3D Visualization (optional) | Three.js (Digital Twin view) |
| Authentication | JWT handling via HTTP-only cookies |
| Build Tool | Vite |
| Deployment | Nginx / Vercel / AWS S3 + CloudFront |

---

## 3. High-Level Frontend Architecture
src/
│
├── assets/ # Images, icons, static files
├── components/ # Reusable UI components
│ ├── common/
│ ├── charts/
│ ├── dashboard/
│ └── layout/
│
├── pages/ # Route-based pages
│ ├── Dashboard.jsx
│ ├── MachineDetails.jsx
│ ├── Analytics.jsx
│ ├── Alerts.jsx
│ └── Login.jsx
│
├── services/ # API + WebSocket services
│ ├── api.js
│ ├── authService.js
│ └── socketService.js
│
├── store/ # Redux/Zustand store
│ ├── slices/
│ └── store.js
│
├── hooks/ # Custom React hooks
│ ├── useAuth.js
│ ├── useMachineData.js
│ └── useWebSocket.js
│
├── utils/ # Helper functions
├── constants/ # App constants
├── routes/ # Route definitions
└── App.jsx

---

## 4. Core Modules

### 4.1 Dashboard Module
- Real-time CNC machine status
- KPI cards (uptime, efficiency, load)
- Live production metrics
- Alerts summary panel

---

### 4.2 Machine Monitoring Module
- Individual machine telemetry view
- RPM, temperature, vibration data
- Live status indicators (Idle / Running / Fault)
- Historical performance graph

---

### 4.3 Digital Twin Visualization Module
- 3D representation of CNC machine
- Real-time movement simulation
- Tool path visualization
- Material removal simulation

---

### 4.4 Analytics Module
- Production efficiency trends
- Downtime analysis
- Predictive maintenance indicators
- AI-generated insights dashboard

---

### 4.5 Alerts & Notifications Module
- Fault detection alerts
- Threshold-based warnings
- Predictive failure notifications
- Notification history log

---

### 4.6 Authentication Module
- Login / Logout
- Role-based access (Admin, Operator, Engineer)
- Token-based session management

---

## 5. Data Flow Architecture
CNC Machine Sensors
↓
IoT Gateway / Backend API
↓
WebSocket + REST API Layer
↓
Frontend Services (socketService + api.js)
↓
State Management (Redux/Zustand)
↓
React Components
↓
UI Rendering (Dashboard, Charts, Twin View)


---

## 6. Real-Time Communication Design

### WebSocket Flow
- Connection established on login
- Subscriptions per machine ID
- Event types:
  - `machine_update`
  - `alert_triggered`
  - `telemetry_stream`

### Example Event Payload
```json
{
  "machineId": "CNC_101",
  "rpm": 3200,
  "temperature": 75,
  "vibration": 0.02,
  "status": "RUNNING",
  "timestamp": "2026-07-05T10:15:00Z"
}
```
### 7. State Management Strategy
Global State
Auth state (user, token, role)
Machine list
Active machine details
Alerts data
Local State
UI toggles
Filters
Chart settings

### 8. Performance Optimizations
Lazy loading of routes
Memoized components (React.memo)
Debounced API calls
WebSocket throttling for telemetry streams
Virtualized lists for machine logs

### 9. UI/UX Design Principles
Industrial dashboard layout (dark theme default)
High contrast for real-time monitoring
Minimal clicks for critical actions
Responsive design for tablet + desktop
Real-time feedback indicators

### 10. Security Considerations
JWT stored in HTTP-only cookies
Role-based route protection
Input validation for all forms
WebSocket authentication handshake
API rate-limit handling

### 11. Future Enhancements
Micro-frontend separation (Dashboard / Analytics / Twin)
WebGL-based full CNC simulation
Offline mode with cached telemetry
AI assistant inside dashboard (chat-based insights)
Mobile companion app

### 12. Summary
The frontend is designed as a real-time industrial control dashboard that integrates tightly with IoT telemetry systems and backend APIs. It focuses on performance, scalability, and visualization-heavy user experience for CNC Digital Twin operations.