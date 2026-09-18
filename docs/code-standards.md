---
title: "Code Standards"
---


Probe follows consistent coding conventions across the backend, web frontend, and mobile application. These standards improve readability, maintainability, and consistency across the codebase.

##  Naming Conventions

### Backend — Python

The FastAPI backend follows standard Python naming conventions.

| Element         | Convention   | Example             |
| --------------- | ------------ | ------------------- |
| Variables       | `snake_case` | `battery_id`        |
| Functions       | `snake_case` | `get_battery()`     |
| Files / Modules | `snake_case` | `battery_router.py` |
| Classes         | `PascalCase` | `BatteryService`    |
| Schemas         | `PascalCase` | `BatteryCreate`     |
| Enums           | `PascalCase` | `BookingStatus`     |

### Frontend — TypeScript / React

The Next.js frontend follows JavaScript and React conventions.

| Element         | Convention       | Example           |
| --------------- | ---------------- | ----------------- |
| Variables       | `camelCase`      | `batteryData`     |
| Functions       | `camelCase`      | `submitBooking()` |
| Components      | `PascalCase`     | `BookingForm`     |
| Types           | `PascalCase`     | `BookingPayload`  |
| Component files | `PascalCase.tsx` | `BookingForm.tsx` |
| Utility files   | `camelCase.ts`   | `api.ts`          |

### Mobile — Flutter / Dart

The Flutter application follows Dart conventions.

| Element   | Convention        | Example                         |
| --------- | ----------------- | ------------------------------- |
| Variables | `camelCase`       | `userRole`                      |
| Functions | `camelCase`       | `handleLogout()`                |
| Classes   | `PascalCase`      | `MetricGroupCard`               |
| Files     | `snake_case.dart` | `device_registration_page.dart` |
| Constants | `camelCase`       | `primaryBlue`                   |

---

##  Folder & File Structure

The backend uses a layered architecture where each layer has a defined responsibility.

```text
probe/
├── models/
├── repositories/
├── services/
├── schemas/
├── routers/
├── database.py
└── main.py
```

| Layer           | Responsibility                                |
| --------------- | --------------------------------------------- |
| `models/`       | SQLAlchemy database models                    |
| `repositories/` | Direct database operations                    |
| `services/`     | Business logic                                |
| `schemas/`      | Pydantic request and response models          |
| `routers/`      | FastAPI routes and HTTP handling              |
| `database.py`   | Database connection and session configuration |
| `main.py`       | FastAPI application configuration             |

New functionality should follow the existing structure.

For example:

```text
Battery Router
      ↓
Battery Service
      ↓
Battery Repository
      ↓
PostgreSQL
```

This keeps routing, business logic, and database operations separated.

---

##  Error Handling

The backend uses explicit `HTTPException` responses for expected API errors.

```python
raise HTTPException(
    status_code=404,
    detail="Battery not found"
)
```

Common status codes include:

| Status | Meaning                            |
| ------ | ---------------------------------- |
| `400`  | Invalid request or input           |
| `401`  | Authentication required or invalid |
| `403`  | Insufficient permissions           |
| `404`  | Resource not found                 |
| `409`  | Resource conflict                  |
| `500`  | Unexpected server error            |

Error messages should be clear enough to support debugging without exposing sensitive information.

---

##  Frontend Error Handling

Frontend API requests should be handled using `try/catch`.

```typescript
try {
    const response = await submitBooking(payload);
    // Handle successful response
} catch (error) {
    console.error("Booking request failed:", error);
    setError("Unable to complete booking request.");
}
```

User-facing errors should provide clear feedback instead of exposing raw backend errors whenever possible.

---

##  Logging

Backend errors and deployment issues can be investigated through application logs.

For the deployed backend:

```bash
heroku logs --tail
```

During frontend development, errors can be logged using:

```typescript
console.error()
```

Logs must not contain sensitive information such as:

* Passwords
* JWT tokens
* Database credentials
* API secrets
* Other private configuration values

---

##  Code Organization Principles

When contributing to Probe:

* Follow the existing project structure.
* Use the appropriate naming convention for each language.
* Keep business logic inside service layers.
* Keep database operations inside repositories.
* Keep API routes inside routers.
* Use schemas for request and response validation.
* Handle expected errors explicitly.
* Reuse existing components and utilities where appropriate.
* Keep changes focused on the feature being implemented.
* Never commit secrets or credentials to the repository.
