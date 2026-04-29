# E-Commerce Platform: Comprehensive Project Report

## 1. Project Overview
The E-Commerce Platform is a robust, full-stack application designed to provide a premium shopping experience across mobile and web. It features a scalable Node.js backend and a high-performance Flutter mobile application, following industry-standard architecture patterns and security best practices.

---

## 2. Project Structure & Architecture

### **2.1 Backend Architecture (Node.js/Express)**
The backend follows a **Modular Layered Architecture**, designed with a transition path towards microservices. It emphasizes separation of concerns, security, and performance.

```text
backend/
├── src/
│   ├── config/             # Environment & service configurations
│   ├── core/               # Shared utilities (Logger, custom errors)
│   ├── middleware/         # Security, Auth, Validation, Error Handling
│   ├── modules/            # Core Business Logic
│   │   ├── controllers/    # Request handlers
│   │   ├── models/         # Mongoose (MongoDB) schemas
│   │   ├── routes/         # Express route definitions
│   │   └── validators/     # Input validation (express-validator)
│   ├── services/           # External service integrations (Redis, Firebase, Mail)
│   └── app.js              # Application initialization
├── public/                 # Static assets & uploads
├── Dockerfile              # Containerization for API
└── docker-compose.yml      # Infrastructure orchestration (API, Mongo, Redis)
```

### **2.2 Frontend Architecture (Flutter)**
The mobile application uses **Feature-First Clean Architecture** combined with **Functional Programming** patterns. It utilizes **Riverpod** for declarative state management.

```text
ecommerce_app/
├── lib/
│   ├── core/               # Shared logic (Router, Theme, Localization, Error handling)
│   ├── features/           # Feature-based modules
│   │   ├── auth/           # Login, Register, Profile
│   │   ├── product/        # Catalog, Search, Details
│   │   ├── cart/           # Cart management
│   │   ├── payment/        # Razorpay integration, Card management
│   │   └── order/          # Order history & Tracking
│   ├── shared/             # Reusable UI components
│   └── main.dart           # Application entry point & service initialization
```

---

## 3. Technology Stack & Justification

| Layer | Technology | Justification |
| :--- | :--- | :--- |
| **Frontend** | **Flutter** | Cross-platform (iOS/Android) with high-performance UI and native-feel animations. |
| **State Management** | **Riverpod** | Compile-safe, testable, and robust state management for Flutter. |
| **Backend Runtime** | **Node.js (ESM)** | Event-driven, non-blocking I/O ideal for scalable real-time e-commerce APIs. |
| **Web Framework** | **Express.js 5.x** | Fast, unopinionated, and minimal framework with a vast middleware ecosystem. |
| **Database** | **MongoDB** | Schema-flexible document database, perfect for evolving product attributes. |
| **Cache & Rate Limit** | **Redis (ioredis)** | High-speed in-memory data store for product caching and distributed rate limiting. |
| **Authentication** | **Firebase & JWT** | Firebase for social login/MFA; JWT for secure, stateless session management. |
| **Payments** | **Razorpay** | Reliable payment gateway for seamless transaction processing in the Indian market. |
| **Real-time Updates** | **Socket.io** | Bi-directional communication for order tracking and instant notifications. |
| **Infrastructure** | **Docker** | Ensures environment consistency and simplifies horizontal scaling. |
| **Observability** | **Sentry & Winston** | Real-time error tracking (Sentry) and structured logging (Winston). |

---

## 4. Key Modules & Responsibilities

### **4.1 Identity & Access (Auth)**
- **Responsibilities**: User registration, multi-factor authentication, session management, and role-based access control (RBAC).
- **Security**: Password hashing with Bcrypt, NoSQL injection protection, and JWT verification.

### **4.2 Product Catalog**
- **Responsibilities**: Hierarchical category management, product searching with filtering/sorting, and inventory tracking.
- **Optimization**: Redis caching for popular products to reduce database load.

### **4.3 Order Management System**
- **Responsibilities**: Cart persistence, checkout flow, order placement, and historical tracking.
- **Workflow**: Validates stock availability -> Calculates discounts (Coupons) -> Processes Payment -> Updates Status.

### **4.4 Real-time Notification Engine**
- **Responsibilities**: Pushing order status updates, promotional alerts, and system notifications.
- **Technologies**: Socket.io for active sessions; Firebase Cloud Messaging (FCM) for background alerts.

---

## 5. Data Flow & System Interactions

1.  **Request Entry**: The Flutter app sends an HTTPS request to the API Gateway (Express).
2.  **Security Gate**: Middleware validates JWT, checks rate limits (Redis), and sanitizes inputs.
3.  **Service Execution**: The Controller calls specialized services (Catalog Service, Payment Service).
4.  **Persistence/Retrieval**: Data is fetched from MongoDB. If caching is enabled, the result is stored in/retrieved from Redis.
5.  **External Sync**: For payments, the backend communicates with Razorpay. For login, it validates tokens with Firebase.
6.  **Response**: The response is compressed and sent back to the client with a Correlation ID for tracing.

---

## 6. Design Decisions & Constraints

### **Design Decisions**
- **Functional Programming (Dartz)**: Used in the Flutter app to handle errors as values (`Either<Failure, Success>`), preventing runtime crashes.
- **Graceful Degradation**: The API is designed to fallback to MemoryStore if Redis is temporarily unavailable.
- **Parallel Startup**: Frontend and Backend services initialize dependencies (Firebase, Hive, Redis) in parallel to minimize startup time.
- **Strict Security**: Implemented Helmet for HTTP headers, HPP for parameter pollution, and JSON size limits to prevent DoS.

### **Assumptions & Constraints**
- **Scalability**: The system assumes horizontal scaling capability via Docker and Redis-backed session management.
- **Localization**: Primarily optimized for the Indian market with support for 15+ local languages.
- **Network**: Assumes intermittent connectivity; implementing Hive (Local DB) in Flutter for offline catalog viewing.
