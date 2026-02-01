# AirBnB Clone - Project Documentation

Comprehensive technical documentation for an AirBnB-style booking platform backend, developed as part of the ALX Software Engineering program.

## Overview

This repository contains detailed specifications, diagrams, and requirements for building a feature-rich AirBnB clone backend system. It includes user authentication, property management, and booking functionality with a focus on security, scalability, and performance.

## Repository Structure

```
alx-airbnb-project-documentation/
├── requirements.md              # Detailed backend requirement specifications
├── features-and-functionalities/ # Feature breakdown and descriptions
├── user-stories/                # User stories for all system actors
├── use-case-diagram/            # UML use case diagrams
├── data-flow-diagram/           # System data flow visualizations
└── flowcharts/                  # Process flowcharts for key features
```

## Core Features

### 1. **User Authentication**
- User registration with role-based access (Guest/Host)
- JWT-based authentication (access & refresh tokens)
- Secure password hashing with bcrypt/argon2

### 2. **Property Management**
- Property listing creation for hosts
- Public property search and filtering
- Location and price-based queries
- Pagination support for scalability

### 3. **Booking System**
- Date-based property reservations
- Conflict detection for overlapping bookings
- Automated pricing calculation
- Transaction-based booking creation

## Technical Specifications

- **API Version:** v1 (prefix: `/api/v1`)
- **Authentication:** JWT tokens (RS256 algorithm)
- **Data Format:** JSON
- **Database:** Relational (PostgreSQL/MySQL recommended)

## Key Performance Requirements

| Feature | Target Performance |
|---------|-------------------|
| User Registration | < 500ms |
| Property Creation | < 300ms |
| Property Search | < 200ms |
| Booking Conflict Check | < 100ms |

## Security Features

- Password hashing and salting (no plain-text storage)
- Role-based authorization (Guest, Host, Admin)
- Short-lived access tokens (15 minutes)
- Long-lived refresh tokens (7 days)
- Atomic transactions for race condition prevention

## Documentation

Refer to [`requirements.md`](./requirements.md) for detailed:
- API endpoint specifications
- Request/response examples
- Validation rules
- Process flowcharts
- Non-functional requirements

## Getting Started

This repository contains **documentation only**. For implementation:

1. Review the [requirements specification](./requirements.md)
2. Examine user stories in the `user-stories/` directory
3. Study the diagrams for system architecture understanding
4. Follow the flowcharts for implementing business logic

## User Roles

- **Guest:** Search properties, create bookings, manage reservations
- **Host:** List properties, manage listings, view bookings
- **Admin:** Full system access and management

## API Endpoints

### Authentication
- `POST /api/v1/auth/register` - User registration
- `POST /api/v1/auth/login` - User login

### Properties
- `POST /api/v1/properties` - Create property (Host only)
- `GET /api/v1/properties` - List/search properties (Public)

### Bookings
- `POST /api/v1/bookings` - Create booking (Guest only)

## Project Context

This documentation is part of the ALX Software Engineering curriculum, focusing on backend system design and API specification best practices.

## License

This project is part of ALX Africa's software engineering program.

---
