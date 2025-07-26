# Backend Requirements Specification – Airbnb Clone

This document outlines the functional and technical requirements for core backend features in the Airbnb Clone project. The goal is to provide clear specifications to guide implementation and ensure consistency across development.

---

## 1. User Authentication

### 🧩 Description

Handles secure login, registration, and session management for guests and hosts.

### 📌 API Endpoints

| Method | Endpoint            | Description                          |
|--------|---------------------|--------------------------------------|
| POST   | /api/auth/register  | Register a new user (guest/host)     |
| POST   | /api/auth/login     | Authenticate user and return JWT     |
| GET    | /api/auth/profile   | Get logged-in user profile           |
| PATCH  | /api/auth/update    | Update profile information           |

### 🔄 Input / Output

#### Register

**Input:**

```json
{
  "email": "user@example.com",
  "password": "securePass123",
  "role": "guest"
}
````

**Output:**

```json
{
  "token": "JWT_TOKEN",
  "user": { "id": "uuid", "email": "user@example.com", "role": "guest" }
}
```

### ✅ Validation Rules

* Email must be unique and valid format
* Password: min 8 characters, include letters + numbers
* Role must be either `guest` or `host`

### 🚀 Performance Criteria

* Auth response time < 300ms under normal load
* JWT tokens expire after 24 hours
* Rate limiting: max 10 login attempts per IP/hour

---

## 2. Property Management

### 🧩 Description

Allows hosts to list, update, and remove properties with details like pricing, availability, and amenities.

### 📌 API Endpoints

| Method | Endpoint             | Description                      |
| ------ | -------------------- | -------------------------------- |
| POST   | /api/properties      | Create a new listing             |
| GET    | /api/properties      | List all properties (searchable) |
| GET    | /api/properties/\:id | Get single property details      |
| PATCH  | /api/properties/\:id | Update property                  |
| DELETE | /api/properties/\:id | Delete property                  |

### 🔄 Input / Output

#### Create Property

**Input:**

```json
{
  "title": "Modern Apartment in Nairobi",
  "description": "Close to CBD, 2 bed 1 bath",
  "location": "Nairobi",
  "price_per_night": 45.00,
  "availability": ["2025-08-01", "2025-08-15"],
  "amenities": ["Wi-Fi", "Parking", "TV"]
}
```

**Output:**

```json
{
  "id": "uuid",
  "status": "success"
}
```

### ✅ Validation Rules

* Price must be numeric and > 0
* Title, description, and location are required
* Availability dates must not overlap with existing bookings

### 🚀 Performance Criteria

* Search response time < 500ms
* Limit listing creation to 20 per host to prevent spam
* Paginate search results (default 10 per page)

---

## 3. Booking System

### 🧩 Description

Allows guests to book properties, manage booking statuses, and view booking history.

### 📌 API Endpoints

| Method | Endpoint                  | Description                      |
| ------ | ------------------------- | -------------------------------- |
| POST   | /api/bookings             | Create a booking request         |
| GET    | /api/bookings             | List bookings for logged-in user |
| PATCH  | /api/bookings/\:id/cancel | Cancel booking                   |
| GET    | /api/bookings/status/\:id | View status (pending/confirmed)  |

### 🔄 Input / Output

#### Create Booking

**Input:**

```json
{
  "property_id": "uuid",
  "start_date": "2025-08-05",
  "end_date": "2025-08-10",
  "guests": 2
}
```

**Output:**

```json
{
  "booking_id": "uuid",
  "status": "pending"
}
```

### ✅ Validation Rules

* Booking dates must not overlap existing bookings
* `guests` must not exceed max allowed per property
* Only users with role `guest` can book

### 🚀 Performance Criteria

* Booking response < 400ms
* Payment integration must handle timeouts/failures gracefully
* Concurrency control to prevent double booking

---

## ✅ Notes

* All endpoints must return proper HTTP status codes (200, 201, 400, 401, 404, 500)
* JSON is the standard for all request and response bodies
* Error messages must be descriptive for debugging
