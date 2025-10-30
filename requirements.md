# AirBnB Clone: Backend Requirement Specifications

This document provides the detailed functional and non-functional requirements for the key backend features of the AirBnB Clone project. 

All API endpoints are assumed to be prefixed with `/api/v1`.

## 1. Feature: User Authentication
This feature covers user registration and login, based on user entity and JWT for authentication.

### 1.1 User Registration
- **User Story:** As a new user, I want to register an account so that I can log in and participate as a guest or host.
- **EndPoint:** `POST /auth/register`
- **Authentication:** `None (Public)
- **Request (Input):** `application/json`
    
***Example***
```json
{
    "first_name": "Lameck",
    "last_name": "Mugo",
    "email": "mugolameck@gmail.com",
    "password": "a-very-strong-password!",
    "role": "guest"
}
```
- **Validation Rules:**
    - `first_name`, `last_name`, `email`, `password`, `role` are *required*.
    - ***`email`*** must be a valid email format and must be **unique** in the `user` table.
    - ***`password`*** must be at least 8 characters long.
    - ***`role`*** must be either `guest` or `host`.

- **Process (Flowchart Logic):**
1. Receive and parse the JSON body.
2. Validate the input data against the rules.
3. If validation fails, return a `400 Bad Request` with error details.
4. Check if a `user` with the given `email` already exists in the `user` table.
5. If `email` exists, return a `400 Bad Request` error.
6. Securely hash the `password` using `bcrypt` or `argon2`.
7. Create a new `User` record in the database with the provided data.
8. Return a `201 Created` response with the new user's details.

***Example: Response (Success: `201 Created`)***
```json
{
    "user_id": "a0eebc99-9c0b-4ef8-bb6d-6bb9bd380a11",
    "email": "mugolameck@gmail.com",
    "message": "User registered successfully."
}
```

***Example: Response (Success: `201 Created`)***
```json
{
    "errors": {
        "email": "This email address is already in use.",
        "password": "Password must be at least 8 characters long."
    }
}
```

- **Non-Functional Requirements:**
    - *Security:* Passwords MUST be hashed and salted. No plain-text passwords in the database.
    - *Performance*: API response (success or fail) must be under 500ms.

### 1.2 User Login

- **User Story:** As a registered user, I want to log in so that I can access my profile and protected features.
- **Endpoint:** `POST /auth/login`
- **Authentication:** `None (Public)`
- Request (Input): `application/json`
***Example***
```json
{
    "email": "mugolameck@gmail.com",
    "password": "a-very-strong-password!"
}
```

- **Validation Rules:**
    - `email` and `password` are *required*.
    - `email` must be a valid email format.

- **Process**
1. Receive and validate the input.
2. Find the `User` in the database by `email`.
3. If `User` not found, return a `401 Unauthorized` error.
4. Compare the provided `password` with the stored `password_hash` using the hashing algorithm's "compare" function.
5. If passwords do not match, return a `401 Unauthorized error.
6. If passwords match, generate a JWT (JSON Web Token) pair:
    - An **Access Token** (short-lived eg 15min)
    - A **Refresh Token** (long-lived, eg 7 days)
7. Return a `200 OK` response with the tokens.

***Example Response (Success: `200 OK`):***
```json
{
    "access_token": "eyJh...[short_token]...c45A",
    "refresh_token": "eyJh...[long_token]...b78x"
}
```
***Response (Error: `401 Unauthorized`):***
```json
{
    "error": "Invalid credentials."
}
```
- **Non-Functional Requirements:**
    - *Security*: Token generation must use a strong secret key (RS256 algorithm). Access tokens must be short-lived to limit attack windows.

---
## 2. Feature: Property Management

This feature covers the creation and listing of properties, based on the `Property` entity.

### 2.1 Create a New Property
- **User Story:** As a Host, I want to list my property so that Guests can find and book it.
- **Endpoint:** `POST /properties`
- **Authentication:** `Required (JWT Access Token)`
    - **Authorization (Role): User's role must be `host` or `admin`.

- **Request (Input):** `application/json`
```json
{
    "name": "Luxury Penthouse with city View",
    "description":"A beautiful 3-bedroom apartment...",
    "location":"Westlands, Nairobi, Kenya",
    "price_per_night":"3000.00 KES"
}
```

- **Validation Rules:**
    - `name`, `description`, `location`, `price_per_night` are *required*.
    - `name` must be less than 100 characters.
    - `price_per_night` must be a positive decimal value.

- **Process**
1. Authenticate the user from the JWT Access Token.
2. If user is not authenticated, return `401 Unauthorized`.
3. If user's role is `guest`, return `403 Forbidden`.
4. Validate the input JSON. If invalid return `400 Bad Request`.
5. Create a new `Property` record in the database.
6. Set the `host_id` on the new property to the authenticated user's `user_id`.
7. Return a `201 Created` response with the new property's data.

***Response (Success: `201 Created`):***
```json
{
    "property_id": "1d09e5b9-1f48-4360-9195-25b848037a11",
    "host_id": "a0eebc99-9c0b-4ef8-bb6d-6bb9bd380a11",
    "name": "Luxury Penthouse with City View",
    "location": "Westlands, Nairobi, Kenya",
    "price_per_night": "2500",
    "created_at": "2025-10-27T02:46:00Z"
}
```
**Response (Error: `403 Forbidden`):**
```json
{
    "error": "You do not have permission to perform this action."
}
```
- **Non-Functional Requirements**
    - *Perfomance*: Property Creation should complete in under 300ms.

### **2.2 List All Properties**

- **User Story:** As a Guest, I want to serach for properties so that I can find available places to stay.
- **Endpoint**: `GET /properties`
- **Authentication:** `None (Public)`
- Request (Input): Query Parameters (Optional)
    - `GET /properties?location=Nairobi`
    - `GET /properties?min_price=1000&max_price=3000`

### - **Process**
1. Start with a query for all records in the `property` table.
2. if a `location` query parameter is provided, filter the query (eg, WHERE location ILIKE '%Nairobi%'). 
3. if `min_price` or `max_price` are provided, add those filters.
4. Execute the final query against the database.
5. Return a `200 OK` response with an array of property data.

***Response (`Success: 200 OK`)***
```json
{
    "count": 1,
    "results": [ 
        {
            "property_id": "1d09e5b9-1f48-4360-9195-25b848037a",
            "name": "Luxury Penthouse with City view",
            "location": "Westlands, Nairobi, Kenya",
            "price_per_night": "2500"
        }
    ]
}
```

- **Non-Functional Requirements:**
    - *Performance*: Response time must be under 200ms.
    - *Scalability:* The endpoint must support *pagination* (eg, *`?page=1&limit=20`*) to handle thousands of listings.
    - *Optimization*: The query must use database *indexes* on `location` and `price_per_night`

    ## 3. Feature: Booking System
    This feature covers the creation of a new booking, based on `Bookings` entity.

    ### 3.1 Create a New Booking
    - *User Story*: As a guest, I want to book a property for specific dates so that my reservation is confirmed.
    - *Endpoint*: `POST /bookings`
    - *Authentication:* `Required (JWT Access Token)`
        - Authorization (Role): User's role must `guest` or `admin`.
    
    - **Request (Input): `application/json`** 
    ```json
    {
        "property_id": "1d09e5b9-1f48-4360-9195-25b848037a",
        "start_date": "2025-12-20",
        "end_date": "2025-12-25"
    }
    ```

- **Validation Rules:**
    - `property_id`, `start_date`, `end_date` are *required*.
    -  `property_id` must exist in the `property` table.
    - `start_date` must be in the future.
    - `end_date` must be after `start_date`.
    - *CRITICAL:* The dates `start_date` to `end_date` must not overlap with any existing `confirmed` booking for that `property_id` in the `booking` table.


### Process
1. Authenticate the user from the JWT. If fail, return `401 Unauthorized`.
2. Authorize the user's role. If `host`, return the `403 Forbidden`.
3. Validate the input JSON. if invalid, return `400 Bad Request`.
4. Query the `booking` table for any confirmed bookings for this requested dates.
5. If an overlap exists, return `409 Conflict` (Conflict with existing resource).
6. Fetch the `price_per_night` from the `property` table.
7. Calculate the `total_price` (eg, `price_per_night` * *No. of nights*)
8. Save the new `booking` record to the database with `status: 'pending'`.
9. `payment` process is triggered.
10. Set `status: 'confirmed'`.
11. Return `201 Created`:
```json
{
  "booking_id": "a1b2c3d4-a111-4444-8888-c0eebc990001",
  "user_id": "b0eebc99-9c0b-4ef8-bb6d-6bb9bd380b22",
  "property_id": "1d09e5b9-1f48-4360-9195-25b848037a11",
  "start_date": "2025-12-20",
  "end_date": "2025-12-25",
  "total_price": 1250.00,
  "status": "confirmed"
}
```
***Response (error: `409 conflict`)***
```json
{
    "error": "These dates are not available for the selected property."
}
```
### Non-Functional Requirements

- **Atomicity:** The check for date conflicts and the creation of the new booking should be an atomic database transaction to prevent race condtions (two users booking the same dates at the same time).
- **Perfomance:** Date conflict-checking must be highly optimized, <100ms.