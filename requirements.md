
# Backend Requirements — Airbnb Clone

This document defines the **technical** and **functional** requirements for the core backend modules of the Airbnb Clone:

- User Authentication  
- Property Management  
- Booking System  


---

## 1. User Authentication

### Functional Overview

- Users can sign up as either a **Guest** or **Host**.
- Authentication supports both **email/password** and **OAuth** (Google/Facebook).
- Passwords are securely stored using hashing.
- Sessions are managed using **JWT tokens**.

### API Endpoints

| Method | Endpoint             | Description            |
|--------|----------------------|------------------------|
| POST   | `/api/auth/register` | Register a new user    |
| POST   | `/api/auth/login`    | Authenticate user      |
| GET    | `/api/auth/profile`  | Retrieve user profile  |
| PUT    | `/api/auth/profile`  | Update user profile    |

### Sample Input & Output

#### Register (`POST /api/auth/register`)

**Request Body:**

```json
{
  "email": "dagem@example.com",
  "password": "SomePassword1!",
  "role": "guest"
}
````

**Response:**

```json
{
  "message": "User registered successfully",
  "access_token": "<jwt_token>"
}
```

### Validation Rules

* Email must be in valid format.
* Password must be at least 8 characters with at least 1 number and 1 uppercase letter.
* Role must be either `"guest"` or `"host"`.

### Performance Targets

* Response time for registration/login < 500ms under normal load.
* JWTs expire after 24 hours.

---

## 2. Property Management

### Functional Overview

* **Hosts** can create, edit, and delete property listings.
* **Guests** can browse and search for properties.
* Listings include title, description, location, price, amenities, availability, and images.

### API Endpoints

| Method | Endpoint              | Description               |
| ------ | --------------------- | ------------------------- |
| POST   | `/api/properties`     | Add a new property        |
| GET    | `/api/properties`     | List available properties |
| GET    | `/api/properties/:id` | View a property detail    |
| PUT    | `/api/properties/:id` | Update property listing   |
| DELETE | `/api/properties/:id` | Delete property listing   |

### Sample Input & Output

#### Create Property (`POST /api/properties`)

**Request Body:**

```json
{
  "title": "Cozy Apartment",
  "description": "2-bedroom apartment in downtown",
  "location": "Casablanca",
  "price_per_night": 80,
  "amenities": ["WiFi", "Pool"],
  "availability": {
    "start_date": "2025-06-01",
    "end_date": "2025-09-01"
  }
}
```

**Response:**

```json
{
  "message": "Property created successfully",
  "property_id": "1234"
}
```

### Validation Rules

* Title is required and must not exceed 150 characters.
* Price must be a positive integer.
* Availability dates must be valid, and `end_date` must come after `start_date`.

### Performance Targets

* Search results return within 1 second for up to 1000 records.
* Support pagination (default: 10 items per page).

---

## 3. Booking System

### Functional Overview

* Guests can book available properties for specific date ranges.
* Prevent overlapping bookings via date conflict checks.
* Booking statuses include: `pending`, `confirmed`, `cancelled`, `completed`.

### API Endpoints

| Method | Endpoint                   | Description          |
| ------ | -------------------------- | -------------------- |
| POST   | `/api/bookings`            | Make a new booking   |
| GET    | `/api/bookings`            | List user's bookings |
| PUT    | `/api/bookings/:id/cancel` | Cancel a booking     |

### Sample Input & Output

#### Create Booking (`POST /api/bookings`)

**Request Body:**

```json
{
  "property_id": "1234",
  "check_in": "2025-06-15",
  "check_out": "2025-06-20"
}
```

**Response:**

```json
{
  "message": "Booking created successfully",
  "booking_id": "5678",
  "status": "pending"
}
```

### Validation Rules

* Check-in and check-out must be valid dates.
* Booking dates must not overlap existing bookings for the property.
* Property must exist and be available during the requested period.

### Performance Targets

* Booking operations complete within 700ms.
* Database enforces date conflict validation with constraints.

---

## General Notes

* All APIs must return proper HTTP status codes: `200 OK`, `201 Created`, `400 Bad Request`, `401 Unauthorized`, `404 Not Found`, `409 Conflict`.
