# poc-fastify-prisma-zod-swagger

> A high-performance REST API engine built on Fastify 5, Prisma ORM 7 with PostgreSQL, runtime schema validation via Zod, and auto-generated Swagger UI OpenAPI documentation.

**Inception (First Commit):** `2023-01-07` • **Batch / Horizon:** `batch-2023` • **Architecture Domain:** `Modern REST Engine / Fastify & Prisma ORM`

---

## 📌 Architecture & Overview

`fastify-prisma-zod-swagger` is an enterprise-grade backend architecture blueprint showcasing end-to-end type safety, high-throughput request serialization, and automated documentation generation. Operating on Fastify 5 and Prisma ORM 7, it leverages `fastify-type-provider-zod` to infer static TypeScript types directly from runtime validation schemas.

The service is structured modularly: controllers, route definitions, schemas, and services are decoupled across domain boundaries (`user`, `product`), while infrastructure plugins (`prisma`, `swagger`, `jwt`, `helmet`, `rate-limit`) are encapsulated using `fastify-plugin`. Graceful shutdown handling is orchestrated via `close-with-grace`.

```
                              +-----------------------------------------------+
                              |          fastify-prisma-zod-swagger           |
                              |                 (Port: 3000)                  |
                              |                                               |
[ Client / Swagger UI ]       |  +--------------------+                       |
       |                      |  | Helmet & RateLimit |                       |
       | HTTP (Bearer JWT)    |  +---------+----------+                       |
       v                      |            |                                  |
+---------------------------+ |  +---------v----------+                       |
| Fastify 5 Engine          +-+->+ Swagger UI (/docs) |                       |
| (Zod Type Provider)       | |  +---------+----------+                       |
+---------------------------+ |            |                                  |
                              |            v                                  |
                              |  +--------------------+                       |
                              |  | Zod Validator &    |                       |
                              |  | SerializerCompiler |                       |
                              |  +---------+----------+                       |
                              |            |                                  |
                              |            v                                  |
                              |  +--------------------+                       |
                              |  | Domain Routers     |                       |
                              |  | (User & Product)   |                       |
                              |  +---------+----------+                       |
                              |            |                                  |
                              |            v                                  |
                              |  +--------------------+  +------------------+ |
                              |  | Prisma 7 Client    |->| PostgreSQL DB    | |
                              |  | (@prisma/client)   |  | (Docker Compose) | |
                              |  +--------------------+  +------------------+ |
                              +-----------------------------------------------+
```

* **Context:** Modern, production-ready REST API reference template engineered to evaluate Fastify's schema compilation capabilities, automated OpenAPI specification generation, and Prisma 7 PostgreSQL integration.
* **Key Patterns:**
  * **Unified Type Provider:** Single-source schema contracts with Zod powering runtime body validation, response serialization, and TypeScript compile-time type inference.
  * **Automated OpenAPI Generation:** Dynamic schema generation via `@fastify/swagger` and `@fastify/swagger-ui` using `jsonSchemaTransform`.
  * **Encapsulated Dependency Injection:** Decorating Fastify server instances with Prisma and authentication helpers via `fastify-plugin`.
  * **Graceful Lifecycle Teardown:** Automated connection termination and process cleanup with `close-with-grace`.

---

## 🛠 Tech Stack

* **Runtime & Framework:** Node.js (>= 20), Fastify 5.8.5, TypeScript 5.x
* **Database & ORM:** PostgreSQL 15, Prisma ORM 7.7.0 (`@prisma/client`, `@prisma/adapter-pg`)
* **Validation & Types:** Zod 3.x, `fastify-type-provider-zod`
* **API Documentation:** `@fastify/swagger` 9.7.0, `@fastify/swagger-ui` 5.2.5
* **Security & Auth:** `@fastify/jwt` 10.0.0, `@fastify/helmet` 13.0.2, `@fastify/rate-limit` 10.3.0, `bcrypt` 6.0.0
* **Lifecycle & Tooling:** `close-with-grace` 2.5.0, Docker Compose, Vitest / Jest, Postman Collections

---

## ✨ Features & Endpoints

* **User Management & Authentication:** Password hashing using bcrypt, credential verification, and JWT session token generation.
* **Product Catalog Service:** Authenticated creation and unauthenticated querying of product inventory.
* **Interactive OpenAPI Explorer:** Live Swagger UI documentation with executable request testing at `/docs`.
* **Containerized Infrastructure:** Full `docker-compose.yml` provisioning PostgreSQL with persistent volume mounts.

### Key API Routes / Interfaces

| Method | Route | Auth | Description |
|---|---|---|---|
| `GET` | `/docs` | Public | Interactive Swagger UI OpenAPI documentation. |
| `POST` | `/api/users` | Public | Register new user with validated email and password. |
| `POST` | `/api/users/login` | Public | Authenticate credentials and return signed JWT. |
| `GET` | `/api/users` | Protected | Retrieve list of registered users (passwords omitted). |
| `POST` | `/api/products` | Protected | Create a new product entry assigned to the authenticated user. |
| `GET` | `/api/products` | Public | Retrieve catalog of all active products. |

---

## 🚀 Getting Started

### Prerequisites

* Node.js `>= 20.0.0`
* Docker & Docker Compose
* pnpm (`npm install -g pnpm`)

### Installation

```bash
# Clone the repository
git clone https://github.com/fabogit/poc-fastify-prisma-zod-swagger.git
cd poc-fastify-prisma-zod-swagger

# Install dependencies
pnpm install
```

### Environment Configuration

Create a `.env` file based on your local setup:

```env
PORT=3000
DATABASE_URL="postgresql://postgres:postgres@localhost:5432/fastify_db?schema=public"
JWT_SECRET="super-secret-jwt-key"
```

### Running External Database

```bash
# Start PostgreSQL database container
docker compose up -d
```

### Database Migration

```bash
# Apply Prisma schema migrations
npx prisma migrate dev
```

### Running the Application

```bash
# Development mode with live reload
pnpm run dev

# Production build and execution
pnpm run build
pnpm run start
```

Navigate to [http://localhost:3000/docs](http://localhost:3000/docs) for the Swagger API documentation.

---

## 📄 License & Author

Developed by [@fabogit](https://github.com/fabogit).
