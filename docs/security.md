---
title: "Security"
---


Probe implements security measures across authentication, password storage, authorization, cross-origin access, and application configuration. These controls protect user accounts, API endpoints, and sensitive system configuration.

##  Password Security

User passwords are never stored as plain text.

Before a password is stored in the database, it is hashed using **bcrypt** through the Passlib library. During authentication, the provided password is compared against the stored hash rather than being stored or retrieved as the original password.

This reduces the risk of exposing user passwords if the database is compromised.

---

##  JWT Authentication

Probe uses **JSON Web Tokens (JWT)** to authenticate requests to protected API endpoints.

When a user successfully logs in through:

```http
POST /users/login
```

the backend generates a signed JWT containing information such as the user's ID and user type.

The token is configured using:

```text
JWT_SECRET_KEY
JWT_ALGORITHM=HS256
JWT_EXPIRE_DAYS=30
```

Protected requests include the token in the HTTP authorization header:

```http
Authorization: Bearer <access_token>
```

The backend validates the token before allowing access to protected resources.

### Authentication Flow

```text
User
  ↓
POST /users/login
  ↓
FastAPI Authentication
  ↓
Credentials Verified
  ↓
JWT Generated
  ↓
Frontend Stores Token
  ↓
Protected API Request
  ↓
JWT Validated
  ↓
Resource Access Granted
```

If the token is missing, invalid, or expired, the API returns an authentication error.

---

##  Role-Based Access Control

Probe uses the `user_type` field to control access to protected functionality.

The main user roles include:

* **ADMIN** – manages administrative resources and platform-level operations.
* **RECYCLER** – registers devices, manages batteries, and works with testing data.
* **UPS_COMPANY** – views available battery inventory and creates battery bookings.

Administrative endpoints use a dedicated authentication dependency that verifies:

```python
user_type == "ADMIN"
```

This prevents users with other roles from accessing admin-only resources.

### Role Access Flow

```text
Authenticated User
        ↓
   JWT Validated
        ↓
    user_type
        ↓
 ┌──────┼─────────────┐
 ↓      ↓             ↓
ADMIN  RECYCLER   UPS_COMPANY
 ↓      ↓             ↓
Admin  Battery/    Inventory/
Access Device      Booking
       Operations  Operations
```

---

##  CORS Protection

Probe uses **Cross-Origin Resource Sharing (CORS)** to control which frontend applications can communicate with the FastAPI backend.

Allowed origins are configured through the `CORS_ORIGINS` environment variable.

Development environments may include:

```text
http://localhost:3000
http://127.0.0.1:3000
```

The deployed frontend can also be added to the allowed origins.

Using an explicit list of origins prevents unrelated websites from making requests to the API.

Example configuration:

```text
CORS_ORIGINS=http://localhost:3000,https://your-production-frontend.com
```

Production deployments should use the actual frontend domain rather than allowing all origins with `*`.

---

##  Environment Variables and Secrets

Sensitive configuration values are kept outside the source code using environment variables.

Probe uses environment variables for values such as:

```text
DATABASE_URL
JWT_SECRET_KEY
JWT_ALGORITHM
JWT_EXPIRE_DAYS
CORS_ORIGINS
```

This prevents database credentials, authentication secrets, and deployment-specific configuration from being hardcoded in the application.

The `.env` file should not be committed to GitHub.

A `.gitignore` entry should therefore include:

```text
.env
.env.local
```

For deployed environments, these values are configured through the hosting platform's environment-variable settings.

---

##  API Protection

Protected API resources require authentication before sensitive operations can be performed.

Examples include:

```text
/users/
/devices/
/batteries/
/bookings/
/v1/sensor-readings/
```

The backend uses authentication dependencies to identify the current user before processing protected requests.

Authorization is then applied where specific roles are required.

This creates two levels of protection:

```text
Authentication
"Who are you?"
       ↓
JWT Validation
       ↓
Authorization
"What are you allowed to access?"
       ↓
Resource
```

---

##  Security Responsibilities

Security is applied across the different layers of the Probe system:

| Layer              | Security Measure                  |
| ------------------ | --------------------------------- |
| User Accounts      | bcrypt password hashing           |
| API Authentication | JWT bearer tokens                 |
| Authorization      | Role-based access control         |
| Frontend ↔ Backend | CORS origin restrictions          |
| Configuration      | Environment variables             |
| Database Access    | Authenticated backend access      |
| API Resources      | Protected routes and dependencies |

Together, these measures provide the baseline security required for Probe's authentication, battery management, telemetry, and booking workflows.
