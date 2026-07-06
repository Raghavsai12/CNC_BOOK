# 15_Security_Architecture.md

# Security Architecture

## Overview

Security is a fundamental aspect of the CNC Digital Twin platform. Since the system processes operational data from industrial CNC machines and provides real-time monitoring capabilities, it must protect users, machine data, APIs, and infrastructure from unauthorized access and cyber threats.

The application follows a **defense-in-depth** strategy, where multiple layers of security work together to reduce risk.

---

# Security Objectives

The security architecture is designed to achieve the following goals:

- Protect user accounts
- Secure machine telemetry
- Prevent unauthorized API access
- Protect sensitive data
- Ensure data integrity
- Maintain system availability
- Support secure communication
- Enable auditing and traceability

---

# Security Layers

```
                    User
                      │
                      ▼
              HTTPS / TLS Encryption
                      │
                      ▼
              Reverse Proxy (Nginx)
                      │
                      ▼
             Express Application
                      │
      ┌───────────────┼────────────────┐
      │               │                │
Authentication   Authorization   Input Validation
      │               │                │
      └───────────────┼────────────────┘
                      ▼
              Business Services
                      │
                      ▼
                  MongoDB
```

---

# Authentication

Authentication verifies the identity of users before granting access.

Implementation:

- JWT (JSON Web Token)
- bcrypt password hashing
- Secure login
- Token expiration
- Protected API routes

Authentication Flow

```
User Login

↓

Credential Validation

↓

Password Verification

↓

JWT Generation

↓

Protected API Access
```

---

# Authorization

The application uses **Role-Based Access Control (RBAC)**.

Roles:

- Admin
- Engineer
- Operator

Permission Flow

```
Incoming Request

↓

JWT Verification

↓

Role Validation

↓

Permission Granted

↓

Controller
```

---

# Password Security

Passwords are never stored in plaintext.

Process

```
Password

↓

bcrypt.hash()

↓

Hashed Password

↓

Database
```

Security Benefits

- Salt generation
- One-way hashing
- Resistant to rainbow table attacks

---

# JWT Security

Each authenticated user receives a signed JWT.

JWT Contains

- User ID
- Email
- Role
- Issue Time
- Expiration Time

Best Practices

- Short-lived access tokens
- Strong secret keys
- Signature verification
- Token expiration
- HTTPS transmission

---

# HTTPS

All communication between clients and servers should occur over HTTPS.

Benefits

- Encryption
- Integrity
- Authentication
- Protection against man-in-the-middle attacks

Flow

```
Browser

↓

TLS Handshake

↓

Encrypted Communication

↓

Server
```

---

# Input Validation

Every incoming request is validated before reaching business logic.

Validation includes:

- Required fields
- Data types
- Length limits
- Numeric ranges
- Email format
- Date format

Example

```
POST /telemetry

↓

Validate Payload

↓

Reject Invalid Requests
```

---

# Input Sanitization

Sanitization helps prevent malicious input.

Examples:

- Trim whitespace
- Escape special characters
- Remove unexpected HTML
- Validate JSON structure

Threats Mitigated

- XSS
- Injection attacks
- Malformed requests

---

# CORS Configuration

Cross-Origin Resource Sharing (CORS) limits which origins can access the API.

Example Policy

```
Allowed Origin

↓

https://dashboard.company.com
```

Only trusted frontend applications are allowed to communicate with the backend.

---

# HTTP Security Headers

The application uses **Helmet** to set secure HTTP headers.

Headers include:

- Content-Security-Policy
- X-Frame-Options
- X-Content-Type-Options
- Referrer-Policy
- Strict-Transport-Security

Benefits

- Clickjacking protection
- MIME sniffing prevention
- Secure browser behavior

---

# Rate Limiting

Rate limiting protects APIs against abuse.

Example Policy

```
100 Requests

↓

1 Minute

↓

Per IP Address
```

Benefits

- Prevent brute-force attacks
- Reduce API abuse
- Protect server resources

---

# Secure Environment Variables

Sensitive configuration is stored in environment variables.

Example

```
JWT_SECRET

MONGO_URI

PORT

CLIENT_URL

BCRYPT_ROUNDS
```

Never commit `.env` files to version control.

---

# Database Security

Database protection measures include:

- Authentication
- Network isolation
- Least privilege access
- Encrypted connections (TLS)
- Regular backups
- Audit logging

---

# Logging & Auditing

Security-relevant events are logged.

Examples:

- Login attempts
- Failed authentication
- Permission denials
- Database errors
- Machine access
- Alert acknowledgments

Audit Log Example

```json
{
  "user": "admin",
  "action": "DELETE_MACHINE",
  "machineId": "CNC-001",
  "timestamp": "2026-07-05T14:00:00Z"
}
```

---

# Error Handling

The application avoids exposing internal implementation details.

Instead of:

```
MongoDB connection failed at line 123
```

Return:

```json
{
  "success": false,
  "message": "Internal server error"
}
```

Detailed errors are written to logs for developers.

---

# API Security

Security mechanisms:

- JWT authentication
- RBAC authorization
- Input validation
- Input sanitization
- HTTPS
- Rate limiting
- Consistent error responses

---

# WebSocket Security

Real-time connections are also secured.

Measures include:

- JWT verification during handshake
- Authorized room joins
- Event payload validation
- TLS (WSS)
- Connection rate limiting

Flow

```
Socket Connect

↓

JWT Verification

↓

Room Authorization

↓

Receive Events
```

---

# File Upload Security (Future)

If file uploads are introduced:

- Validate file type
- Restrict file size
- Scan for malware
- Store outside the web root
- Generate unique filenames

---

# Common Security Threats

| Threat | Mitigation |
|---------|------------|
| SQL/NoSQL Injection | Input validation and parameterized queries |
| Cross-Site Scripting (XSS) | Output encoding and sanitization |
| Cross-Site Request Forgery (CSRF) | CSRF tokens / HTTP-only cookies |
| Brute Force | Rate limiting and account lockout |
| Credential Theft | bcrypt hashing and HTTPS |
| Token Theft | Short-lived JWTs and secure storage |
| Clickjacking | X-Frame-Options |
| MITM Attack | HTTPS/TLS |
| Broken Access Control | RBAC |
| Sensitive Data Exposure | Encryption and least privilege |

---

# OWASP Alignment

The application addresses common OWASP Top 10 risks by implementing:

- Strong authentication
- Access control
- Secure configuration
- Input validation
- Dependency management
- Logging and monitoring
- Cryptographic protection

---

# Backup & Disaster Recovery

Security also includes data resilience.

Strategy:

- Daily incremental backups
- Weekly full backups
- Off-site storage
- Restore testing
- MongoDB replica sets

---

# Security Monitoring

Metrics monitored include:

- Failed logins
- Suspicious API activity
- Rate-limit violations
- WebSocket failures
- Authentication errors
- Database access anomalies

---

# Future Enhancements

Planned improvements:

- Multi-Factor Authentication (MFA)
- OAuth 2.0 / OpenID Connect
- Single Sign-On (SSO)
- Secrets management with Vault
- Web Application Firewall (WAF)
- Intrusion Detection System (IDS)
- Security Information and Event Management (SIEM)
- Automated vulnerability scanning

---

# Interview Talking Points

If asked about security, discuss:

- Why passwords are hashed instead of encrypted.
- The difference between authentication and authorization.
- Why JWT is used for stateless APIs.
- How RBAC simplifies permission management.
- Why HTTPS is mandatory for production.
- The importance of input validation and sanitization.
- How rate limiting protects APIs.
- Why Helmet and secure HTTP headers are important.
- How audit logs help with incident investigation.
- How the design aligns with OWASP best practices.

---

# Summary

The CNC Digital Twin platform adopts a layered security architecture combining JWT authentication, RBAC authorization, HTTPS, input validation, secure headers, rate limiting, and comprehensive logging. These controls protect users, APIs, machine telemetry, and infrastructure while supporting a scalable and maintainable industrial application suitable for production deployments.