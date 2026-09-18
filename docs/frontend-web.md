---
title: "Frontend Web"
---


The Probe web application is built with **Next.js** and provides the main interface for interacting with the battery testing, inventory, device, and booking systems.

<div className="probe-features" style="grid-template-columns: 1fr; max-width: 700px; margin: 24px auto;">
  <div className="probe-card">
    <img src="/images/frontend-web.jpg" alt="Frontend Web overview" className="probe-screenshot" />
    <h3>Frontend Web Dashboard</h3>
  </div>
</div>

---

##  Frontend Setup

Navigate to the dashboard project:

```bash
cd HERckers_Dashboard
```

Install the required dependencies:

```bash
npm install
```

### Environment Variables

Create a `.env.local` file in the project root:

```env
NEXT_PUBLIC_API_URL=http://localhost:8000
```

For the deployed application, `NEXT_PUBLIC_API_URL` should point to the hosted backend API.

### Run the Dashboard

Start the development server:

```bash
npm run dev
```

The dashboard will be available at:

```text
http://localhost:3000
```

---

##  Architecture Pattern

### Web — Next.js

The Probe web application is built using **Next.js** with the **App Router**.

The application is organized into routes, reusable components, API communication utilities, and MQTT functionality to support maintainability and simple onboarding.

### Application Structure

```text
app/
├── api/
│   ├── mqtt/
│   │   └── route.ts
│   ├── booking-dashboard/
│   │   └── booking.types.ts
│   ├── bookingreport/
│   ├── dashboard/
│   ├── device-registry/
│   ├── inventory/
│   │   └── lib/
│   │       └── api.ts
│   └── mqtt.ts
│
├── booking-dashboard/
│   ├── LocalNav.tsx
│   └── page.tsx
│
├── bookingreport/
│   └── page.tsx
│
├── dashboard/
│   └── page.tsx
│
├── device-registry/
│   └── page.tsx
│
├── inventory/
│   └── page.tsx
│
├── livedata/
│   └── page.tsx
│
├── login/
│   └── page.tsx
│
├── profile/
│   └── page.tsx
│
├── report/
│   └── page.tsx
│
├── signup/
│   └── page.tsx
│
├── components/
│   ├── auth-form.module.css
│   ├── battery-dashboard.tsx
│   ├── booking-nav.tsx
│   ├── BookingForm.tsx
│   ├── device-registered-modal.tsx
│   ├── device-registry.tsx
│   ├── inventory-dashboard.tsx
│   ├── landing-page.module.css
│   ├── landing-page.tsx
│   ├── login-form.tsx
│   ├── main-content.tsx
│   ├── profile-page.module.css
│   ├── profile-page.tsx
│   ├── RecyclerCard.tsx
│   ├── register-device-form.tsx
│   ├── sidebar.tsx
│   └── signup-form.tsx
│
├── lib/
│   ├── api.ts
│   └── mqtt.ts
│
├── favicon.ico
├── globals.css
└── layout.tsx
```

The structure separates application routes from reusable UI components and communication utilities.

---

##  State Propagation, Token Management and Route Security

### Network Communication Core

The project routes external communication requests through unified client abstractions in:

```text
app/lib/api.ts
```

This allows authentication information to be added automatically to protected API requests.

The authentication header is generated using the stored JWT:

```typescript
const getAuthHeaders = (): Record<string, string> => {
  const token =
    typeof window !== "undefined"
      ? localStorage.getItem("probe_token")
      : null;

  return {
    "Content-Type": "application/json",
    ...(token && {
      "Authorization": `Bearer ${token}`,
    }),
  };
};
```

### Booking Request

Booking creation is also handled through the centralized API communication layer:

```typescript
export async function submitBookingCreation(
  payload: BookingPayload
): Promise<Response> {
  const targetUrl =
    `${process.env.NEXT_PUBLIC_API_URL}/bookings/`;

  const response = await fetch(targetUrl, {
    method: "POST",
    headers: getAuthHeaders(),
    body: JSON.stringify(payload),
  });

  if (!response.ok) {
    const errorDetails = await response.json();

    throw new Error(
      errorDetails.detail ||
      "Fails to establish reservation request transaction."
    );
  }

  return response.json();
}
```

The resulting flow is:

```text
User
 ↓
Frontend Component
 ↓
app/lib/api.ts
 ↓
JWT Authorization Header
 ↓
FastAPI Backend
 ↓
Booking Endpoint
```

---

##  Navigational Rails & Route Security

### Context-Aware Sub-Navigation

The booking dashboard uses:

```text
app/booking-dashboard/LocalNav.tsx
```

The component uses client-side rendering where required:

```typescript
"use client";
```

It provides local navigation within the booking dashboard while maintaining the dashboard's overall layout.

The navigation styling uses Tailwind CSS configurations, including:

```text
bg-[#EAF4FC]
```

and responsive layout dimensions such as:

```text
w-full
md:w-[220px]
```

This allows the navigation rail to adapt to different screen sizes.

### State & Token Lifecycles

Authentication tokens are stored in the browser's local storage.

The application retrieves the stored token when making authenticated requests:

```typescript
localStorage.getItem("token")
```

The token is then included in the request:

```text
Authorization: Bearer <token>
```

This allows the frontend to maintain the user's authenticated session while communicating with protected backend endpoints.

> **Implementation note:** The codebase contains references to both `probe_token` and `token`. The documentation should ultimately use the key implemented by the current authentication flow consistently.

---

##  Unified Network Client Abstraction

Frontend components should not implement independent raw `fetch` pipelines for backend communication.

Instead, requests are routed through the shared API abstraction located in:

```text
app/lib/api.ts
```

The API client is responsible for:

* Constructing backend request URLs.
* Attaching authentication tokens.
* Sending request payloads.
* Handling API responses.
* Standardizing error handling.

This creates a consistent communication layer between the Next.js frontend and the FastAPI backend.

```text
┌─────────────────────────┐
│     Frontend Component  │
└────────────┬────────────┘
             │
             ▼
┌─────────────────────────┐
│      app/lib/api.ts     │
│                         │
│  • Authentication       │
│  • API Requests         │
│  • Error Handling       │
└────────────┬────────────┘
             │
             ▼
┌─────────────────────────┐
│      FastAPI Backend    │
└─────────────────────────┘
```

Centralizing network communication reduces duplicated request logic and provides a consistent approach to authentication and API error handling throughout the application.
