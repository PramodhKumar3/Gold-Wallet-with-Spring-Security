# 🏦 GoldWallet Monolith Web Application with Spring Security + JWT

A **secure Spring Boot 3.x monolith application** for managing digital gold transactions.  
It uses **Spring Security + JWT tokens** for authentication and role-based access control (RBAC) with three roles:

- 👤 **User**
- 🛒 **Vendor**
- 🛠️ **Admin**

This README explains setup, JWT security flow, endpoints, and future enhancements in a **beginner-friendly, detailed way**.

---

## 📑 Table of Contents

- [Project Overview](#-project-overview)
- [Technology Stack](#-technology-stack)
- [Project Structure](#-project-structure)
- [Application Configuration](#-application-configuration)
- [Authentication & Authorization with JWT](#-authentication--authorization-with-jwt)
- [Endpoints Overview](#-endpoints-overview)
- [Prerequisites](#-prerequisites)
- [Run Instructions](#-run-instructions)
- [Future Enhancements](#-future-enhancements)
- [Contact](#-contact)

---

## 📌 Project Overview

**GoldWallet Monolith Web Application** provides a secure platform for managing digital gold holdings.

It allows:

- Users to manage their digital and physical gold wallets.
- Vendors to onboard and provide gold services.
- Admins to monitor and control the entire ecosystem.

With **Spring Security + JWT** integration, the system ensures:  
✔️ **Secure Authentication** — Only valid users with correct credentials can access APIs.  
✔️ **Role-Based Authorization** — Different roles (User, Vendor, Admin) get access to only what they need.  
✔️ **Stateless Security** — No session storage. Each request is verified using a JWT token.

---

## 💻 Technology Stack

- **Java 17** — Programming language
- **Spring Boot 3.x** — Backend framework for REST APIs and web UI
- **Spring Security** — Provides authentication & authorization
- **JWT (JSON Web Token)** — Used for stateless authentication
- **MySQL** — Database for storing entities
- **Maven** — Build & dependency management
- **Thymeleaf + Bootstrap** — Basic web interface for testing UI

---

## 📂 Project Structure

```
GoldWalletApplication/
├── src/main/java/com/...
│   ├── controllers/        # REST & MVC endpoints
│   ├── services/           # Business logic
│   ├── repositories/       # Database layer
│   ├── security/           # JWT filters, utils, configs
│   └── entities/           # User, Vendor, Transaction, Wallet
├── src/main/resources/
│   ├── templates/          # Thymeleaf HTML pages
│   ├── static/             # CSS/JS
│   └── application.properties
├── pom.xml                 # Maven dependencies
└── README.md               # Documentation
```

**Key Security Components**:

- `AuthenticationController` → Exposes `/authenticate` endpoint to login and get token.
- `JwtRequestFilter` → Validates every incoming request for a valid token.
- `JwtUtil` → Utility class for generating, parsing, and validating JWTs.
- `UserDetailsServiceImpl` → Loads user details (currently in-memory; can be extended to DB).

---

## ⚙ Application Configuration

Sample `application.properties` configuration:

```bash
spring.application.name=GoldWalletJwtApplication
server.port=8082

# MySQL Database Config
spring.datasource.url=jdbc:mysql://localhost:3306/digitalgoldwallet
spring.datasource.username=root
spring.datasource.password=your_password
spring.jpa.hibernate.ddl-auto=update

# JWT Config
jwt.secret=yourSecretKeyHere
jwt.expiration=3600000   # 1 hour (in milliseconds)
```

---

## 🔐 Authentication & Authorization with JWT

JWT authentication works in **3 simple steps**:

### 1️⃣ Login to Generate Token

Make a POST request to:

```bash
POST http://localhost:8082/authenticate
```

Request Body:

```json
{ "username": "user", "password": "password" }
```

Response:

```json
{ "jwtToken": "eyJhbGciOiJIUzI1NiJ9..." }
```

This token will be used for subsequent requests.

---

### 2️⃣ Use Token in Subsequent Requests

For every secured API request, add the following HTTP header:

```
Authorization: Bearer <jwtToken>
```

---

### 3️⃣ Access Secured APIs Based on Role

Once the token is added, the server verifies:

- If the token is valid.
- If the user has permission (role-based access).

If both checks pass → API executes successfully.  
If not → `403 Forbidden` or `401 Unauthorized`.

---

## 🌐 Endpoints Overview

### 🔑 Authentication

- `POST /authenticate` → Generate JWT token

---

### 👤 User Role (ROLE_USER)

**Login Example:**

```json
{ "username": "user", "password": "password" }
```

**Allowed Endpoints:**

- GET `/api/v2/users/check_balance/{userId}`
- GET `/api/v2/users/{id}/virtual_gold_holdings`
- GET `/api/v2/users/{id}/physical_gold_holding`
- GET `/api/v2/users/{id}/transaction_history`
- GET `/api/v2/users/{id}/payments`
- PUT `/api/v2/users/update/{id}`
- PUT `/api/v2/users/{id}/update_address/{addressId}`
- POST `/api/v2/virtual_gold_holding/convertToPhysical/{id}`

---

### 🛒 Vendor Role (ROLE_VENDOR)

**Login Example:**

```json
{ "username": "vendor", "password": "vendorpass" }
```

**Allowed Endpoints:**

- GET/POST/PUT/DELETE `/api/v2/vendor/**`
- GET/POST/PUT/DELETE `/api/v2/vendor_branches/**`
- GET `/api/v2/virtual_gold_holding/byUserAndVendor/{userId}/{vendorId}`

---

### 🛠️ Admin Role (ROLE_ADMIN)

**Login Example:**

```json
{ "username": "admin", "password": "adminpass" }
```

**Allowed Endpoints (full access):**

- `/api/v2/users/**`
- `/api/v2/address/**`
- `/api/v2/vendor/**`
- `/api/v2/vendor_branches/**`
- `/api/v2/virtual_gold_holding/**`
- `/api/v2/physical_gold_transactions/**`
- `/api/v2/payments/**`
- `/api/v2/transaction_history/**`

👉 **Admin has unrestricted access.**

---

## 📋 Prerequisites

- Install **Java 17+**
- Install **Maven 3.x**
- Setup **MySQL** with schema: `digitalgoldwallet`
- Install **Postman** (or use `curl`) to test JWT authentication

---

## ▶ Run Instructions

### Method - Run from IDE (IntelliJ/Eclipse/STS)

- Import project as a Maven project.
- Update Maven dependencies.
- Run `GoldWalletApplication.java` as a **Spring Boot App**.
- Access app at:  
  `http://localhost:8082`

---

## 🚀 Future Enhancements

### 1️⃣ Entity-Based Microservices

- Break down the monolith into services like User Service, Vendor Service, Wallet Service, Transaction Service.
- Each with its own database, enabling independent scaling.

### 2️⃣ Stronger Security

- Replace in-memory users with DB-based authentication.
- Use **BCrypt** password hashing.
- Add **refresh tokens** for long-lived sessions.
- Implement **two-factor authentication (2FA)**.

### 3️⃣ Advanced RBAC

- Define fine-grained permissions for each role.
- Allow vendors partial control over users they serve.

### 4️⃣ Reporting & Analytics

- Generate **downloadable reports (PDF/Excel)**.
- Add admin dashboards with **charts & insights** for gold trends.

---

## 📬 Contact

👨‍💻 Created with 💻 by [Pramodh Kumar](https://www.linkedin.com/in/tamminaina-pramodh-kumar-6433a4242)

For issues or contributions, please open a GitHub issue or reach out directly.
