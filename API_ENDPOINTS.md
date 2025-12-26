# API Endpoints Documentation
## University Bus Tracker System

**Version:** 1.0  
**Date:** December 26, 2025  
**Base URL:** `https://api.university-bus-tracker.com/v1`

---

## Table of Contents

1. [API Overview](#1-api-overview)
2. [Authentication](#2-authentication)
3. [User Management](#3-user-management)
4. [Bus Tracking](#4-bus-tracking)
5. [Routes & Stops](#5-routes--stops)
6. [Bus Passes](#6-bus-passes)
7. [Payments](#7-payments)
8. [Trips & Driver Operations](#8-trips--driver-operations)
9. [Notifications](#9-notifications)
10. [Admin Operations](#10-admin-operations)
11. [Analytics & Reports](#11-analytics--reports)
12. [WebSocket Events](#12-websocket-events)

---

## 1. API Overview

### 1.1 General Information

- **Protocol:** HTTPS
- **Format:** JSON
- **Authentication:** JWT Bearer Token
- **Rate Limiting:** 100 requests/minute per user
- **API Version:** v1

### 1.2 Standard Response Format

**Success Response:**
```json
{
  "success": true,
  "message": "Operation successful",
  "data": { },
  "meta": {
    "timestamp": "2025-12-26T10:30:00Z",
    "request_id": "uuid-here"
  }
}
```

**Error Response:**
```json
{
  "success": false,
  "error": {
    "code": "ERROR_CODE",
    "message": "Error description",
    "details": []
  },
  "meta": {
    "timestamp": "2025-12-26T10:30:00Z",
    "request_id": "uuid-here"
  }
}
```

**Paginated Response:**
```json
{
  "success": true,
  "data": [],
  "pagination": {
    "page": 1,
    "limit": 20,
    "total": 100,
    "total_pages": 5,
    "has_next": true,
    "has_prev": false
  }
}
```

### 1.3 HTTP Status Codes

- `200 OK` - Successful request
- `201 Created` - Resource created successfully
- `400 Bad Request` - Invalid request data
- `401 Unauthorized` - Authentication required
- `403 Forbidden` - Insufficient permissions
- `404 Not Found` - Resource not found
- `422 Unprocessable Entity` - Validation error
- `429 Too Many Requests` - Rate limit exceeded
- `500 Internal Server Error` - Server error

---

## 2. Authentication

### 2.1 Register User

**Endpoint:** `POST /auth/register`

**Description:** Register a new user account

**Request Body:**
```json
{
  "email": "student@university.edu",
  "password": "SecurePassword123!",
  "password_confirm": "SecurePassword123!",
  "first_name": "Ali",
  "last_name": "Ahmed",
  "phone": "+92-300-1234567",
  "role": "student",
  "student_id": "CS-2021-001"
}
```

**Response:** `201 Created`
```json
{
  "success": true,
  "message": "Registration successful. Please verify your email.",
  "data": {
    "user": {
      "id": "uuid",
      "email": "student@university.edu",
      "email_verified": false,
      "role": "student"
    }
  }
}
```

---

### 2.2 Verify Email

**Endpoint:** `POST /auth/verify-email`

**Request Body:**
```json
{
  "token": "verification-token-here"
}
```

**Response:** `200 OK`
```json
{
  "success": true,
  "message": "Email verified successfully"
}
```

---

### 2.3 Login

**Endpoint:** `POST /auth/login`

**Request Body:**
```json
{
  "email": "student@university.edu",
  "password": "SecurePassword123!"
}
```

**Response:** `200 OK`
```json
{
  "success": true,
  "message": "Login successful",
  "data": {
    "user": {
      "id": "uuid",
      "email": "student@university.edu",
      "role": "student",
      "profile": {
        "first_name": "Ali",
        "last_name": "Ahmed",
        "avatar_url": "https://..."
      }
    },
    "tokens": {
      "access_token": "jwt-token-here",
      "refresh_token": "refresh-token-here",
      "expires_in": 3600
    }
  }
}
```

---

### 2.4 Refresh Token

**Endpoint:** `POST /auth/refresh`

**Request Body:**
```json
{
  "refresh_token": "refresh-token-here"
}
```

**Response:** `200 OK`
```json
{
  "success": true,
  "data": {
    "access_token": "new-jwt-token",
    "refresh_token": "new-refresh-token",
    "expires_in": 3600
  }
}
```

---

### 2.5 Logout

**Endpoint:** `POST /auth/logout`

**Headers:** `Authorization: Bearer {token}`

**Response:** `200 OK`

---

### 2.6 Forgot Password

**Endpoint:** `POST /auth/forgot-password`

**Request Body:**
```json
{
  "email": "student@university.edu"
}
```

**Response:** `200 OK`

---

### 2.7 Reset Password

**Endpoint:** `POST /auth/reset-password`

**Request Body:**
```json
{
  "token": "reset-token-here",
  "password": "NewPassword123!",
  "password_confirm": "NewPassword123!"
}
```

**Response:** `200 OK`

---

## 3. User Management

### 3.1 Get Current User Profile

**Endpoint:** `GET /users/me`

**Headers:** `Authorization: Bearer {token}`

**Response:** `200 OK`
```json
{
  "success": true,
  "data": {
    "id": "uuid",
    "email": "student@university.edu",
    "role": "student",
    "profile": {
      "first_name": "Ali",
      "last_name": "Ahmed",
      "phone": "+92-300-1234567",
      "avatar_url": "https://..."
    },
    "student_info": {
      "student_id": "CS-2021-001",
      "department": "Computer Science",
      "year_of_study": 3
    }
  }
}
```

---

### 3.2 Update Profile

**Endpoint:** `PATCH /users/me`

**Headers:** `Authorization: Bearer {token}`

**Request Body:**
```json
{
  "first_name": "Ali",
  "last_name": "Ahmed",
  "phone": "+92-300-1234567",
  "date_of_birth": "2001-05-15",
  "address": "Street Address"
}
```

**Response:** `200 OK`

---

### 3.3 Upload Avatar

**Endpoint:** `POST /users/me/avatar`

**Headers:** 
- `Authorization: Bearer {token}`
- `Content-Type: multipart/form-data`

**Request Body:** (Form Data)
- `avatar`: File (image)

**Response:** `200 OK`
```json
{
  "success": true,
  "data": {
    "avatar_url": "https://storage.../avatar.jpg"
  }
}
```

---

### 3.4 Change Password

**Endpoint:** `POST /users/me/change-password`

**Headers:** `Authorization: Bearer {token}`

**Request Body:**
```json
{
  "current_password": "OldPassword123!",
  "new_password": "NewPassword123!",
  "new_password_confirm": "NewPassword123!"
}
```

**Response:** `200 OK`

---

## 4. Bus Tracking

### 4.1 Get All Active Buses

**Endpoint:** `GET /buses/active`

**Headers:** `Authorization: Bearer {token}`

**Query Parameters:**
- `route_id` (optional): Filter by route
- `status` (optional): Filter by status

**Response:** `200 OK`
```json
{
  "success": true,
  "data": [
    {
      "id": "uuid",
      "registration_number": "ISB-123",
      "model": "Toyota Coaster",
      "capacity": 30,
      "current_location": {
        "latitude": 33.6844,
        "longitude": 73.0479,
        "speed": 45.5,
        "bearing": 180.0,
        "timestamp": "2025-12-26T10:30:00Z"
      },
      "route": {
        "id": "uuid",
        "name": "Main Campus to City",
        "code": "R-01",
        "color": "#3B82F6"
      },
      "driver": {
        "id": "uuid",
        "name": "Ahmed Khan"
      },
      "status": "on_route",
      "passengers": 15
    }
  ]
}
```

---

### 4.2 Get Bus Details

**Endpoint:** `GET /buses/:busId`

**Headers:** `Authorization: Bearer {token}`

**Response:** `200 OK`
```json
{
  "success": true,
  "data": {
    "id": "uuid",
    "registration_number": "ISB-123",
    "model": "Toyota Coaster",
    "manufacturer": "Toyota",
    "year": 2020,
    "capacity": 30,
    "color": "White",
    "fuel_type": "diesel",
    "status": "active",
    "mileage": 50000,
    "image_url": "https://...",
    "last_maintenance_date": "2025-11-15",
    "next_maintenance_date": "2026-02-15"
  }
}
```

---

### 4.3 Get Bus Location History

**Endpoint:** `GET /buses/:busId/location-history`

**Headers:** `Authorization: Bearer {token}`

**Query Parameters:**
- `start_date`: ISO 8601 date
- `end_date`: ISO 8601 date
- `trip_id` (optional): Specific trip

**Response:** `200 OK`
```json
{
  "success": true,
  "data": [
    {
      "latitude": 33.6844,
      "longitude": 73.0479,
      "speed": 45.5,
      "bearing": 180.0,
      "timestamp": "2025-12-26T10:30:00Z"
    }
  ]
}
```

---

### 4.4 Get Bus ETA to Stop

**Endpoint:** `GET /buses/:busId/eta/:stopId`

**Headers:** `Authorization: Bearer {token}`

**Response:** `200 OK`
```json
{
  "success": true,
  "data": {
    "bus_id": "uuid",
    "stop_id": "uuid",
    "eta_minutes": 8,
    "eta_timestamp": "2025-12-26T10:38:00Z",
    "distance_km": 3.5,
    "current_location": {
      "latitude": 33.6844,
      "longitude": 73.0479
    }
  }
}
```

---

## 5. Routes & Stops

### 5.1 Get All Routes

**Endpoint:** `GET /routes`

**Headers:** `Authorization: Bearer {token}`

**Query Parameters:**
- `is_active` (optional): true/false
- `search` (optional): Search by name

**Response:** `200 OK`
```json
{
  "success": true,
  "data": [
    {
      "id": "uuid",
      "name": "Main Campus to City",
      "code": "R-01",
      "description": "Route from main campus to city center",
      "color": "#3B82F6",
      "is_active": true,
      "start_point": "Main Campus Gate",
      "end_point": "City Center",
      "distance": 15.5,
      "estimated_duration": 30,
      "frequency": "daily",
      "start_time": "07:00:00",
      "end_time": "22:00:00"
    }
  ]
}
```

---

### 5.2 Get Route Details with Stops

**Endpoint:** `GET /routes/:routeId`

**Headers:** `Authorization: Bearer {token}`

**Response:** `200 OK`
```json
{
  "success": true,
  "data": {
    "id": "uuid",
    "name": "Main Campus to City",
    "code": "R-01",
    "description": "Route description",
    "color": "#3B82F6",
    "distance": 15.5,
    "estimated_duration": 30,
    "stops": [
      {
        "id": "uuid",
        "name": "Main Campus Gate",
        "code": "S-01",
        "order": 1,
        "latitude": 33.6844,
        "longitude": 73.0479,
        "address": "University Main Gate",
        "landmark": "Near Library",
        "estimated_arrival_time": "08:00:00"
      },
      {
        "id": "uuid",
        "name": "Library Stop",
        "code": "S-02",
        "order": 2,
        "latitude": 33.6850,
        "longitude": 73.0485,
        "estimated_arrival_time": "08:10:00"
      }
    ]
  }
}
```

---

### 5.3 Get All Stops

**Endpoint:** `GET /stops`

**Headers:** `Authorization: Bearer {token}`

**Query Parameters:**
- `latitude` (optional): Center latitude
- `longitude` (optional): Center longitude
- `radius` (optional): Radius in km

**Response:** `200 OK`

---

### 5.4 Get Stop Details

**Endpoint:** `GET /stops/:stopId`

**Headers:** `Authorization: Bearer {token}`

**Response:** `200 OK`
```json
{
  "success": true,
  "data": {
    "id": "uuid",
    "name": "Main Campus Gate",
    "code": "S-01",
    "latitude": 33.6844,
    "longitude": 73.0479,
    "address": "University Main Gate",
    "landmark": "Near Library",
    "routes": [
      {
        "id": "uuid",
        "name": "Main Campus to City",
        "code": "R-01"
      }
    ],
    "upcoming_buses": [
      {
        "bus_id": "uuid",
        "registration_number": "ISB-123",
        "eta_minutes": 8
      }
    ]
  }
}
```

---

## 6. Bus Passes

### 6.1 Get Pass Types

**Endpoint:** `GET /passes/types`

**Headers:** `Authorization: Bearer {token}`

**Response:** `200 OK`
```json
{
  "success": true,
  "data": [
    {
      "type": "daily",
      "name": "Daily Pass",
      "description": "Valid for 24 hours",
      "price": 100,
      "currency": "PKR",
      "validity_days": 1
    },
    {
      "type": "weekly",
      "name": "Weekly Pass",
      "description": "Valid for 7 days",
      "price": 600,
      "currency": "PKR",
      "validity_days": 7
    },
    {
      "type": "monthly",
      "name": "Monthly Pass",
      "description": "Valid for 30 days",
      "price": 2000,
      "currency": "PKR",
      "validity_days": 30
    },
    {
      "type": "semester",
      "name": "Semester Pass",
      "description": "Valid for 5 months",
      "price": 8000,
      "currency": "PKR",
      "validity_days": 150
    }
  ]
}
```

---

### 6.2 Get My Passes

**Endpoint:** `GET /passes/my-passes`

**Headers:** `Authorization: Bearer {token}`

**Query Parameters:**
- `status` (optional): active/expired/cancelled

**Response:** `200 OK`
```json
{
  "success": true,
  "data": [
    {
      "id": "uuid",
      "pass_type": "monthly",
      "price": 2000,
      "currency": "PKR",
      "purchase_date": "2025-12-01T10:00:00Z",
      "activation_date": "2025-12-01T10:00:00Z",
      "expiry_date": "2025-12-31T23:59:59Z",
      "status": "active",
      "qr_code": "encrypted-qr-data",
      "qr_code_url": "https://storage.../qr-code.png",
      "usage_count": 45,
      "days_remaining": 5
    }
  ]
}
```

---

### 6.3 Get Active Pass

**Endpoint:** `GET /passes/active`

**Headers:** `Authorization: Bearer {token}`

**Response:** `200 OK`
```json
{
  "success": true,
  "data": {
    "id": "uuid",
    "pass_type": "monthly",
    "expiry_date": "2025-12-31T23:59:59Z",
    "status": "active",
    "qr_code": "encrypted-qr-data",
    "qr_code_url": "https://...",
    "days_remaining": 5,
    "usage_today": 2
  }
}
```

---

### 6.4 Purchase Pass

**Endpoint:** `POST /passes/purchase`

**Headers:** `Authorization: Bearer {token}`

**Request Body:**
```json
{
  "pass_type": "monthly",
  "payment_method": "card"
}
```

**Response:** `201 Created`
```json
{
  "success": true,
  "message": "Pass purchase initiated",
  "data": {
    "pass_id": "uuid",
    "payment_intent": {
      "id": "payment-intent-id",
      "amount": 2000,
      "currency": "PKR",
      "payment_url": "https://kuickpay.com/checkout/..."
    }
  }
}
```

---

### 6.5 Verify Pass (Driver Only)

**Endpoint:** `POST /passes/verify`

**Headers:** `Authorization: Bearer {token}`

**Request Body:**
```json
{
  "qr_code": "encrypted-qr-data"
}
```

**Response:** `200 OK`
```json
{
  "success": true,
  "data": {
    "is_valid": true,
    "pass": {
      "id": "uuid",
      "pass_type": "monthly",
      "expiry_date": "2025-12-31T23:59:59Z",
      "status": "active"
    },
    "student": {
      "id": "uuid",
      "name": "Ali Ahmed",
      "student_id": "CS-2021-001",
      "photo_url": "https://..."
    }
  }
}
```

---

## 7. Payments

### 7.1 Create Payment Intent

**Endpoint:** `POST /payments/intent`

**Headers:** `Authorization: Bearer {token}`

**Request Body:**
```json
{
  "amount": 2000,
  "currency": "PKR",
  "pass_type": "monthly",
  "payment_method": "card"
}
```

**Response:** `201 Created`
```json
{
  "success": true,
  "data": {
    "payment_id": "uuid",
    "client_secret": "payment-secret",
    "payment_url": "https://kuickpay.com/checkout/..."
  }
}
```

---

### 7.2 Payment Webhook (KuickPay)

**Endpoint:** `POST /payments/webhook`

**Description:** Webhook endpoint for payment gateway callbacks

**Request Body:** (From KuickPay)
```json
{
  "event": "payment.success",
  "transaction_id": "txn_123456",
  "amount": 2000,
  "currency": "PKR",
  "metadata": {
    "pass_id": "uuid",
    "student_id": "uuid"
  }
}
```

**Response:** `200 OK`

---

### 7.3 Get Payment History

**Endpoint:** `GET /payments/history`

**Headers:** `Authorization: Bearer {token}`

**Query Parameters:**
- `page`: Page number
- `limit`: Items per page
- `status`: Filter by status

**Response:** `200 OK`
```json
{
  "success": true,
  "data": [
    {
      "id": "uuid",
      "amount": 2000,
      "currency": "PKR",
      "payment_method": "card",
      "status": "completed",
      "transaction_id": "txn_123456",
      "payment_date": "2025-12-01T10:00:00Z",
      "receipt_url": "https://..."
    }
  ],
  "pagination": {
    "page": 1,
    "limit": 20,
    "total": 50,
    "total_pages": 3
  }
}
```

---

### 7.4 Download Receipt

**Endpoint:** `GET /payments/:paymentId/receipt`

**Headers:** `Authorization: Bearer {token}`

**Response:** PDF file download

---

## 8. Trips & Driver Operations

### 8.1 Get Driver Dashboard

**Endpoint:** `GET /drivers/dashboard`

**Headers:** `Authorization: Bearer {token}`

**Roles:** Driver

**Response:** `200 OK`
```json
{
  "success": true,
  "data": {
    "driver_info": {
      "id": "uuid",
      "name": "Ahmed Khan",
      "license_number": "LIC-123456"
    },
    "today_trips": [
      {
        "id": "uuid",
        "route": "Main Campus to City",
        "bus": "ISB-123",
        "start_time": "08:00:00",
        "status": "scheduled"
      }
    ],
    "active_trip": null,
    "stats": {
      "total_trips_today": 4,
      "completed_trips": 2,
      "total_passengers_scanned": 156
    }
  }
}
```

---

### 8.2 Start Trip

**Endpoint:** `POST /trips/start`

**Headers:** `Authorization: Bearer {token}`

**Roles:** Driver

**Request Body:**
```json
{
  "bus_id": "uuid",
  "route_id": "uuid",
  "start_odometer": 50000
}
```

**Response:** `201 Created`
```json
{
  "success": true,
  "message": "Trip started successfully",
  "data": {
    "trip_id": "uuid",
    "bus_id": "uuid",
    "route_id": "uuid",
    "start_time": "2025-12-26T08:00:00Z",
    "status": "in_progress"
  }
}
```

---

### 8.3 Update Trip Location (GPS)

**Endpoint:** `POST /trips/:tripId/location`

**Headers:** `Authorization: Bearer {token}`

**Roles:** Driver

**Request Body:**
```json
{
  "latitude": 33.6844,
  "longitude": 73.0479,
  "speed": 45.5,
  "bearing": 180.0,
  "accuracy": 5.0,
  "altitude": 500.0,
  "timestamp": "2025-12-26T08:05:00Z"
}
```

**Response:** `200 OK`

---

### 8.4 Scan Bus Pass (QR)

**Endpoint:** `POST /trips/:tripId/scan`

**Headers:** `Authorization: Bearer {token}`

**Roles:** Driver

**Request Body:**
```json
{
  "qr_code": "encrypted-qr-data",
  "latitude": 33.6844,
  "longitude": 73.0479
}
```

**Response:** `200 OK`
```json
{
  "success": true,
  "message": "Valid pass",
  "data": {
    "scan_id": "uuid",
    "is_valid": true,
    "student": {
      "name": "Ali Ahmed",
      "student_id": "CS-2021-001",
      "photo_url": "https://..."
    },
    "pass": {
      "type": "monthly",
      "expiry_date": "2025-12-31T23:59:59Z"
    }
  }
}
```

---

### 8.5 Report Delay

**Endpoint:** `POST /trips/:tripId/delay`

**Headers:** `Authorization: Bearer {token}`

**Roles:** Driver

**Request Body:**
```json
{
  "delay_minutes": 15,
  "reason": "Heavy traffic on main road"
}
```

**Response:** `200 OK`

---

### 8.6 End Trip

**Endpoint:** `POST /trips/:tripId/end`

**Headers:** `Authorization: Bearer {token}`

**Roles:** Driver

**Request Body:**
```json
{
  "end_odometer": 50035,
  "notes": "Trip completed successfully"
}
```

**Response:** `200 OK`
```json
{
  "success": true,
  "message": "Trip ended successfully",
  "data": {
    "trip_id": "uuid",
    "start_time": "2025-12-26T08:00:00Z",
    "end_time": "2025-12-26T08:35:00Z",
    "distance_traveled": 35.0,
    "total_passengers": 28,
    "duration_minutes": 35
  }
}
```

---

### 8.7 Get Trip History

**Endpoint:** `GET /trips/history`

**Headers:** `Authorization: Bearer {token}`

**Roles:** Driver, Admin

**Query Parameters:**
- `driver_id` (optional, admin only)
- `bus_id` (optional)
- `start_date` (optional)
- `end_date` (optional)
- `page`, `limit`

**Response:** `200 OK`
```json
{
  "success": true,
  "data": [
    {
      "id": "uuid",
      "route": "Main Campus to City",
      "bus": "ISB-123",
      "driver": "Ahmed Khan",
      "start_time": "2025-12-26T08:00:00Z",
      "end_time": "2025-12-26T08:35:00Z",
      "status": "completed",
      "distance_traveled": 35.0,
      "total_passengers": 28,
      "delay_minutes": 5
    }
  ],
  "pagination": {}
}
```

---

## 9. Notifications

### 9.1 Get Notifications

**Endpoint:** `GET /notifications`

**Headers:** `Authorization: Bearer {token}`

**Query Parameters:**
- `is_read` (optional): true/false
- `type` (optional): Filter by type
- `page`, `limit`

**Response:** `200 OK`
```json
{
  "success": true,
  "data": [
    {
      "id": "uuid",
      "type": "bus_arrival",
      "title": "Bus Arriving Soon",
      "message": "Bus ISB-123 will arrive at your stop in 5 minutes",
      "data": {
        "bus_id": "uuid",
        "stop_id": "uuid",
        "eta_minutes": 5
      },
      "is_read": false,
      "sent_at": "2025-12-26T08:25:00Z",
      "priority": "high"
    }
  ],
  "pagination": {}
}
```

---

### 9.2 Mark Notification as Read

**Endpoint:** `PATCH /notifications/:notificationId/read`

**Headers:** `Authorization: Bearer {token}`

**Response:** `200 OK`

---

### 9.3 Mark All as Read

**Endpoint:** `POST /notifications/mark-all-read`

**Headers:** `Authorization: Bearer {token}`

**Response:** `200 OK`

---

### 9.4 Delete Notification

**Endpoint:** `DELETE /notifications/:notificationId`

**Headers:** `Authorization: Bearer {token}`

**Response:** `200 OK`

---

### 9.5 Get Notification Preferences

**Endpoint:** `GET /notifications/preferences`

**Headers:** `Authorization: Bearer {token}`

**Response:** `200 OK`
```json
{
  "success": true,
  "data": {
    "bus_arrival": {
      "enabled": true,
      "push": true,
      "email": false
    },
    "pass_expiry": {
      "enabled": true,
      "push": true,
      "email": true
    },
    "payment": {
      "enabled": true,
      "push": true,
      "email": true
    },
    "delay": {
      "enabled": true,
      "push": true,
      "email": false
    }
  }
}
```

---

### 9.6 Update Notification Preferences

**Endpoint:** `PATCH /notifications/preferences`

**Headers:** `Authorization: Bearer {token}`

**Request Body:**
```json
{
  "bus_arrival": {
    "enabled": true,
    "push": true,
    "email": false
  }
}
```

**Response:** `200 OK`

---

## 10. Admin Operations

### 10.1 Admin Dashboard Stats

**Endpoint:** `GET /admin/dashboard`

**Headers:** `Authorization: Bearer {token}`

**Roles:** Admin

**Response:** `200 OK`
```json
{
  "success": true,
  "data": {
    "overview": {
      "total_students": 5234,
      "active_students": 4890,
      "total_drivers": 45,
      "active_buses": 38,
      "active_routes": 12
    },
    "today_stats": {
      "total_trips": 156,
      "completed_trips": 142,
      "active_trips": 8,
      "total_passengers": 3456,
      "revenue": 125000
    },
    "active_buses_count": 8,
    "recent_activities": []
  }
}
```

---

### 10.2 Get All Users

**Endpoint:** `GET /admin/users`

**Headers:** `Authorization: Bearer {token}`

**Roles:** Admin

**Query Parameters:**
- `role`: student/driver/admin
- `search`: Search by name or email
- `is_active`: true/false
- `page`, `limit`

**Response:** `200 OK`
```json
{
  "success": true,
  "data": [
    {
      "id": "uuid",
      "email": "student@university.edu",
      "role": "student",
      "is_active": true,
      "email_verified": true,
      "profile": {
        "name": "Ali Ahmed",
        "phone": "+92-300-1234567"
      },
      "created_at": "2025-01-15T10:00:00Z"
    }
  ],
  "pagination": {}
}
```

---

### 10.3 Create User

**Endpoint:** `POST /admin/users`

**Headers:** `Authorization: Bearer {token}`

**Roles:** Admin

**Request Body:**
```json
{
  "email": "newuser@university.edu",
  "password": "TempPassword123!",
  "role": "driver",
  "first_name": "Ahmed",
  "last_name": "Khan",
  "phone": "+92-300-9876543"
}
```

**Response:** `201 Created`

---

### 10.4 Update User

**Endpoint:** `PATCH /admin/users/:userId`

**Headers:** `Authorization: Bearer {token}`

**Roles:** Admin

**Request Body:**
```json
{
  "is_active": false,
  "role": "driver"
}
```

**Response:** `200 OK`

---

### 10.5 Delete User

**Endpoint:** `DELETE /admin/users/:userId`

**Headers:** `Authorization: Bearer {token}`

**Roles:** Admin

**Response:** `200 OK`

---

### 10.6 Manage Buses

**Endpoint:** `GET /admin/buses`

**Headers:** `Authorization: Bearer {token}`

**Roles:** Admin

**Query Parameters:**
- `status`: active/inactive/maintenance
- `search`
- `page`, `limit`

**Response:** `200 OK`

---

### 10.7 Create Bus

**Endpoint:** `POST /admin/buses`

**Headers:** `Authorization: Bearer {token}`

**Roles:** Admin

**Request Body:**
```json
{
  "registration_number": "ISB-126",
  "model": "Coaster",
  "manufacturer": "Toyota",
  "year": 2023,
  "capacity": 30,
  "color": "White",
  "fuel_type": "diesel"
}
```

**Response:** `201 Created`

---

### 10.8 Update Bus

**Endpoint:** `PATCH /admin/buses/:busId`

**Headers:** `Authorization: Bearer {token}`

**Roles:** Admin

**Response:** `200 OK`

---

### 10.9 Delete Bus

**Endpoint:** `DELETE /admin/buses/:busId`

**Headers:** `Authorization: Bearer {token}`

**Roles:** Admin

**Response:** `200 OK`

---

### 10.10 Manage Routes

**Endpoint:** `GET /admin/routes`

**Headers:** `Authorization: Bearer {token}`

**Roles:** Admin

**Response:** `200 OK`

---

### 10.11 Create Route

**Endpoint:** `POST /admin/routes`

**Headers:** `Authorization: Bearer {token}`

**Roles:** Admin

**Request Body:**
```json
{
  "name": "New Route",
  "code": "R-05",
  "description": "Route description",
  "color": "#10B981",
  "start_point": "Point A",
  "end_point": "Point B",
  "distance": 10.5,
  "estimated_duration": 25,
  "frequency": "weekdays",
  "start_time": "07:00:00",
  "end_time": "22:00:00",
  "stops": [
    {
      "stop_id": "uuid",
      "order": 1,
      "estimated_arrival_time": "07:15:00"
    }
  ]
}
```

**Response:** `201 Created`

---

### 10.12 Assign Bus to Route

**Endpoint:** `POST /admin/assignments`

**Headers:** `Authorization: Bearer {token}`

**Roles:** Admin

**Request Body:**
```json
{
  "bus_id": "uuid",
  "route_id": "uuid",
  "driver_id": "uuid",
  "assigned_date": "2025-12-26",
  "start_time": "07:00:00",
  "end_time": "22:00:00"
}
```

**Response:** `201 Created`

---

### 10.13 Send Announcement

**Endpoint:** `POST /admin/announcements`

**Headers:** `Authorization: Bearer {token}`

**Roles:** Admin

**Request Body:**
```json
{
  "title": "System Maintenance Notice",
  "content": "System will be under maintenance on Sunday from 2 AM to 4 AM",
  "target_audience": "all",
  "priority": "high",
  "publish_date": "2025-12-26T10:00:00Z",
  "expiry_date": "2025-12-29T00:00:00Z"
}
```

**Response:** `201 Created`

---

### 10.14 Process Refund

**Endpoint:** `POST /admin/payments/:paymentId/refund`

**Headers:** `Authorization: Bearer {token}`

**Roles:** Admin

**Request Body:**
```json
{
  "reason": "Pass cancelled by student request",
  "amount": 2000
}
```

**Response:** `200 OK`

---

## 11. Analytics & Reports

### 11.1 Get Usage Analytics

**Endpoint:** `GET /analytics/usage`

**Headers:** `Authorization: Bearer {token}`

**Roles:** Admin

**Query Parameters:**
- `start_date`: ISO date
- `end_date`: ISO date
- `granularity`: day/week/month

**Response:** `200 OK`
```json
{
  "success": true,
  "data": {
    "period": {
      "start": "2025-12-01",
      "end": "2025-12-26"
    },
    "total_trips": 3456,
    "total_passengers": 89234,
    "active_users": 4890,
    "most_used_route": {
      "id": "uuid",
      "name": "Main Campus to City",
      "trip_count": 890
    },
    "peak_hours": [
      {"hour": 8, "trip_count": 234},
      {"hour": 17, "trip_count": 289}
    ],
    "daily_stats": [
      {
        "date": "2025-12-01",
        "trips": 156,
        "passengers": 3456
      }
    ]
  }
}
```

---

### 11.2 Get Revenue Analytics

**Endpoint:** `GET /analytics/revenue`

**Headers:** `Authorization: Bearer {token}`

**Roles:** Admin

**Query Parameters:**
- `start_date`, `end_date`

**Response:** `200 OK`
```json
{
  "success": true,
  "data": {
    "period": {
      "start": "2025-12-01",
      "end": "2025-12-26"
    },
    "total_revenue": 2456000,
    "revenue_by_pass_type": [
      {
        "pass_type": "monthly",
        "count": 1234,
        "revenue": 2468000
      }
    ],
    "payment_methods": [
      {
        "method": "card",
        "count": 980,
        "percentage": 79.4
      }
    ],
    "daily_revenue": [
      {
        "date": "2025-12-01",
        "revenue": 125000,
        "transactions": 62
      }
    ]
  }
}
```

---

### 11.3 Get Bus Performance Report

**Endpoint:** `GET /analytics/bus-performance`

**Headers:** `Authorization: Bearer {token}`

**Roles:** Admin

**Query Parameters:**
- `bus_id` (optional)
- `start_date`, `end_date`

**Response:** `200 OK`
```json
{
  "success": true,
  "data": [
    {
      "bus_id": "uuid",
      "registration_number": "ISB-123",
      "total_trips": 234,
      "total_distance": 4567.8,
      "total_passengers": 6789,
      "average_passengers_per_trip": 29,
      "utilization_percentage": 96.7,
      "on_time_percentage": 87.5,
      "delay_minutes_total": 340
    }
  ]
}
```

---

### 11.4 Get Driver Performance Report

**Endpoint:** `GET /analytics/driver-performance`

**Headers:** `Authorization: Bearer {token}`

**Roles:** Admin

**Query Parameters:**
- `driver_id` (optional)
- `start_date`, `end_date`

**Response:** `200 OK`
```json
{
  "success": true,
  "data": [
    {
      "driver_id": "uuid",
      "name": "Ahmed Khan",
      "total_trips": 156,
      "completed_trips": 154,
      "completion_rate": 98.7,
      "total_distance": 2345.6,
      "total_passengers": 4321,
      "on_time_trips": 140,
      "on_time_percentage": 89.7,
      "average_delay_minutes": 3.2
    }
  ]
}
```

---

### 11.5 Export Report

**Endpoint:** `POST /analytics/export`

**Headers:** `Authorization: Bearer {token}`

**Roles:** Admin

**Request Body:**
```json
{
  "report_type": "usage",
  "format": "csv",
  "start_date": "2025-12-01",
  "end_date": "2025-12-26",
  "filters": {
    "route_id": "uuid"
  }
}
```

**Response:** `200 OK`
```json
{
  "success": true,
  "data": {
    "download_url": "https://storage.../report_20251226.csv",
    "expires_at": "2025-12-27T10:00:00Z"
  }
}
```

---

## 12. WebSocket Events

### 12.1 Connection

**URL:** `wss://api.university-bus-tracker.com/ws`

**Authentication:** Query parameter `?token=jwt-token`

---

### 12.2 Client Events

#### Subscribe to Bus Updates

```json
{
  "event": "subscribe_bus",
  "data": {
    "bus_id": "uuid"
  }
}
```

#### Subscribe to Route Updates

```json
{
  "event": "subscribe_route",
  "data": {
    "route_id": "uuid"
  }
}
```

#### Subscribe to Stop Updates

```json
{
  "event": "subscribe_stop",
  "data": {
    "stop_id": "uuid"
  }
}
```

#### Unsubscribe

```json
{
  "event": "unsubscribe",
  "data": {
    "type": "bus",
    "id": "uuid"
  }
}
```

---

### 12.3 Server Events

#### Bus Location Update

```json
{
  "event": "bus_location_update",
  "data": {
    "bus_id": "uuid",
    "latitude": 33.6844,
    "longitude": 73.0479,
    "speed": 45.5,
    "bearing": 180.0,
    "timestamp": "2025-12-26T10:30:00Z",
    "passengers": 28
  }
}
```

#### Bus Status Update

```json
{
  "event": "bus_status_update",
  "data": {
    "bus_id": "uuid",
    "status": "delayed",
    "delay_minutes": 10,
    "reason": "Heavy traffic"
  }
}
```

#### Trip Started

```json
{
  "event": "trip_started",
  "data": {
    "trip_id": "uuid",
    "bus_id": "uuid",
    "route_id": "uuid",
    "start_time": "2025-12-26T08:00:00Z"
  }
}
```

#### Trip Ended

```json
{
  "event": "trip_ended",
  "data": {
    "trip_id": "uuid",
    "bus_id": "uuid",
    "end_time": "2025-12-26T08:35:00Z",
    "total_passengers": 28
  }
}
```

#### Bus Arrival Alert

```json
{
  "event": "bus_arrival_alert",
  "data": {
    "bus_id": "uuid",
    "stop_id": "uuid",
    "eta_minutes": 5,
    "message": "Bus arriving in 5 minutes"
  }
}
```

#### New Notification

```json
{
  "event": "notification",
  "data": {
    "id": "uuid",
    "type": "pass_expiry",
    "title": "Pass Expiring Soon",
    "message": "Your monthly pass will expire in 3 days",
    "priority": "normal"
  }
}
```

---

## 13. Error Codes

| Code | Message | Description |
|------|---------|-------------|
| `AUTH_001` | Invalid credentials | Email or password incorrect |
| `AUTH_002` | Email not verified | User must verify email first |
| `AUTH_003` | Account locked | Too many failed login attempts |
| `AUTH_004` | Token expired | JWT token has expired |
| `AUTH_005` | Invalid token | JWT token is invalid |
| `USER_001` | User not found | User does not exist |
| `USER_002` | Email already exists | Email is already registered |
| `PASS_001` | Pass not found | Bus pass does not exist |
| `PASS_002` | Pass expired | Bus pass has expired |
| `PASS_003` | Invalid QR code | QR code is invalid or corrupted |
| `PAYMENT_001` | Payment failed | Payment transaction failed |
| `PAYMENT_002` | Insufficient funds | Not enough balance |
| `BUS_001` | Bus not found | Bus does not exist |
| `BUS_002` | Bus not available | Bus is under maintenance |
| `ROUTE_001` | Route not found | Route does not exist |
| `TRIP_001` | Trip not found | Trip does not exist |
| `TRIP_002` | Active trip exists | Driver already has an active trip |
| `PERM_001` | Insufficient permissions | User lacks required permissions |
| `RATE_001` | Rate limit exceeded | Too many requests |
| `VALID_001` | Validation error | Input validation failed |
| `SERVER_001` | Internal server error | Unexpected server error |

---

**END OF API ENDPOINTS DOCUMENT**

