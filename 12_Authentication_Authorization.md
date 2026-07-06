# 12_Authentication_Authorization.md

# Authentication & Authorization

## Overview

Authentication and Authorization are critical security components of the CNC Digital Twin platform.

- **Authentication** verifies the identity of a user.
- **Authorization** determines what an authenticated user is allowed to access or modify.

The platform uses **JWT (JSON Web Token)** for stateless authentication and **Role-Based Access Control (RBAC)** for authorization.

---

# Security Goals

The authentication system is designed to provide:

- Secure user login
- Stateless authentication
- Password protection
- API security
- Role-based permissions
- Secure communication
- Protection against unauthorized access

---

# Technologies Used

| Component | Technology |
|------------|------------|
| Authentication | JWT |
| Password Hashing | bcrypt |
| Authorization | RBAC |
| Transport Security | HTTPS |
| Token Storage | HTTP-only Cookies (recommended) / Authorization Header |
| Validation | express-validator |

---

# Authentication Flow

```
User Login
     │
     ▼
Enter Email & Password
     │
     ▼
Backend Validation
     │
     ▼
Password Verification
     │
     ▼
JWT Token Generated
     │
     ▼
Token Sent to Client
     │
     ▼
Client Stores Token
     │
     ▼
Protected API Requests
     │
     ▼
JWT Verification Middleware
     │
     ▼
Controller Access
```

---

# Login Sequence Diagram

```
User
 │
 │ Login Request
 ▼
Frontend
 │
 ▼
Backend
 │
 ▼
Database
 │
 ▲
User Found
 │
 ▼
Password Check
 │
 ▼
JWT Generated
 │
 ▼
Frontend Receives Token
 │
 ▼
Future API Requests
```

---

# User Registration

Registration Process

```
Receive Request

↓

Validate Input

↓

Check Duplicate Email

↓

Hash Password

↓

Save User

↓

Return Success
```

Example Request

```json
{
    "name":"John",
    "email":"john@example.com",
    "password":"password123"
}
```

---

# Password Hashing

Passwords are **never stored in plain text**.

Example Flow

```
User Password

↓

bcrypt.hash()

↓

Hashed Password

↓

Database
```

Example

```
password123

↓

$2b$10$QH1V...
```

Benefits

- One-way hashing
- Salt generation
- Resistant to rainbow table attacks

---

# Login Verification

```
Login Request

↓

Find User

↓

bcrypt.compare()

↓

Password Match?

↓

Generate JWT

↓

Return Token
```

---

# JWT Structure

A JWT consists of three parts.

```
HEADER

.

PAYLOAD

.

SIGNATURE
```

Example

```
xxxxx.yyyyy.zzzzz
```

---

# JWT Payload

Example

```json
{
    "userId":"123",
    "email":"john@example.com",
    "role":"admin",
    "iat":123456,
    "exp":123999
}
```

---

# Authorization Header

Protected API requests include:

```
Authorization

Bearer JWT_TOKEN
```

Example

```
Authorization: Bearer eyJhbGciOi...
```

---

# Authentication Middleware

Flow

```
Incoming Request

↓

Read Authorization Header

↓

Extract Token

↓

Verify JWT

↓

Attach User Object

↓

Next Middleware

↓

Controller
```

Pseudo Code

```javascript
const token = req.headers.authorization;

jwt.verify(token);

req.user = decodedUser;

next();
```

---

# Role-Based Access Control (RBAC)

The application supports three primary roles.

| Role | Description |
|------|-------------|
| Admin | Full system access |
| Engineer | Machine management and analytics |
| Operator | Machine monitoring and operational control |

---

# Authorization Matrix

| Feature | Admin | Engineer | Operator |
|----------|:-----:|:---------:|:--------:|
| View Dashboard | ✅ | ✅ | ✅ |
| Register Machine | ✅ | ✅ | ❌ |
| Delete Machine | ✅ | ❌ | ❌ |
| View Telemetry | ✅ | ✅ | ✅ |
| Resolve Alerts | ✅ | ✅ | ❌ |
| User Management | ✅ | ❌ | ❌ |
| Analytics | ✅ | ✅ | View Only |

---

# Protected Routes

Example

```
GET /api/machines

↓

JWT Middleware

↓

Role Middleware

↓

Controller
```

---

# Authorization Middleware

```
Request

↓

JWT Verification

↓

Role Check

↓

Permission Granted?

↓

Controller

OR

403 Forbidden
```

Pseudo Code

```javascript
if(user.role !== "admin")
{
    return res.status(403);
}
```

---

# Token Expiration

Example

```
Access Token

15 Minutes
```

Future Enhancement

```
Refresh Token

7 Days
```

Benefits

- Reduced attack window
- Better session management
- Improved security

---

# Logout Flow

```
Client Logout

↓

Delete Token

↓

Invalidate Session (optional)

↓

Redirect Login
```

---

# Password Reset Workflow

```
Forgot Password

↓

Email Verification

↓

Temporary Reset Token

↓

Reset Password

↓

Hash New Password

↓

Save Database
```

---

# Security Best Practices

The backend follows several security practices:

- Password hashing with bcrypt
- JWT expiration
- HTTPS communication
- Input validation
- Output sanitization
- CORS configuration
- Rate limiting
- Helmet security headers
- Environment variables
- Strong password policy

---

# Common Authentication Errors

| Error | Status Code |
|---------|-------------|
| Invalid Credentials | 401 |
| Missing Token | 401 |
| Expired Token | 401 |
| Invalid Token | 401 |
| Forbidden Resource | 403 |

---

# Environment Variables

```
JWT_SECRET=

JWT_EXPIRES_IN=

BCRYPT_ROUNDS=

CLIENT_URL=

MONGO_URI=
```

---

# Authentication Lifecycle

```
Register User

↓

Hash Password

↓

Store User

↓

Login

↓

Generate JWT

↓

Client Stores Token

↓

Protected APIs

↓

JWT Verification

↓

Role Authorization

↓

Business Logic
```

---

# Security Threats Addressed

| Threat | Protection |
|---------|------------|
| Password Theft | bcrypt Hashing |
| Token Tampering | JWT Signature |
| Unauthorized Access | RBAC |
| Brute Force | Rate Limiting |
| MITM Attack | HTTPS |
| Injection Attacks | Input Validation |
| XSS | Output Encoding |
| CSRF | HTTP-only Cookies / CSRF Token |

---

# Authentication Architecture

```
             Client
                │
      Login Request
                │
                ▼
         Express Server
                │
                ▼
      Authentication Controller
                │
                ▼
      Password Verification
                │
                ▼
          JWT Service
                │
                ▼
       Authentication Middleware
                │
                ▼
        Protected Resources
```

---

# Future Enhancements

- OAuth 2.0 Login
- Google Authentication
- Microsoft Azure AD Integration
- Multi-Factor Authentication (MFA)
- Refresh Tokens
- Device Management
- Session Revocation
- Audit Logs
- Single Sign-On (SSO)

---

# Interview Talking Points

If asked about authentication during an interview, highlight:

- Why JWT is suitable for stateless APIs.
- Why bcrypt is used instead of encryption for passwords.
- Difference between authentication and authorization.
- How RBAC simplifies permission management.
- Why HTTP-only cookies improve security over localStorage.
- How token expiration limits exposure if a token is compromised.
- Importance of centralized authentication middleware.

---

# Summary

The CNC Digital Twin platform implements a secure authentication and authorization system using JWT, bcrypt, and RBAC. The architecture ensures that only authenticated users can access protected resources, while role-based permissions enforce fine-grained access control. The design follows modern backend security practices and is scalable for future enhancements such as OAuth, MFA, and SSO.