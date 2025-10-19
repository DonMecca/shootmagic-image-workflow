# Backend Structure Document

This document explains the backend setup for the **shootmagic-image-workflow** project. It covers how the server is organized, how data is stored, how the API works, and how everything is hosted and secured. You don’t need a deep technical background to follow along.

## 1. Backend Architecture

- **Monolithic Server with Serverless Functions**
  - We use Next.js’s built-in serverless functions (API Routes) to handle all backend logic. Each function lives under `pages/api` (or `app/api` in the App Router) and scales automatically.

- **Layered Design**
  - Route Handlers (API Routes) receive incoming HTTP requests.
  - Service Layer contains business logic (for example, image processing workflows or user management).
  - Data Access Layer (via Drizzle ORM) reads and writes data to the database.

- **Scalability, Maintainability, Performance**
  - Serverless functions auto-scale based on traffic—no need to manage servers.
  - A clear separation of concerns (routes → services → data) makes it easy to add features or fix bugs.
  - Cold starts are minimal because Next.js keeps recently used functions warm.

## 2. Database Management

- **Database Technology**
  - PostgreSQL (a relational SQL database).
  - Drizzle ORM (a TypeScript-friendly library) to write database queries in code rather than raw SQL.

- **Data Structure & Access**
  - Data is organized into tables (e.g., users, sessions, image_records, presets).
  - We use migrations to evolve the schema over time.
  - Backups are scheduled regularly (daily snapshots).

- **Best Practices**
  - All database credentials are stored in environment variables—never in code.
  - Connection pooling is handled by the hosting provider (e.g., AWS RDS or Neon) to optimize performance.

## 3. Database Schema

Below is an example of the core SQL schema. It’s written in plain SQL so it’s easy to read and modify.

```sql
-- Table for registered users
CREATE TABLE users (
  id SERIAL PRIMARY KEY,
  email VARCHAR(255) UNIQUE NOT NULL,
  hashed_password VARCHAR(255) NOT NULL,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Table for session tokens (Better Auth)
CREATE TABLE sessions (
  id SERIAL PRIMARY KEY,
  user_id INTEGER REFERENCES users(id) ON DELETE CASCADE,
  token VARCHAR(255) UNIQUE NOT NULL,
  expires_at TIMESTAMP NOT NULL
);

-- Table for image processing records
CREATE TABLE image_records (
  id SERIAL PRIMARY KEY,
  user_id INTEGER REFERENCES users(id) ON DELETE SET NULL,
  input_url TEXT NOT NULL,
  output_url TEXT NOT NULL,
  preset VARCHAR(100) NOT NULL,
  status VARCHAR(50) NOT NULL,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Table for available presets
CREATE TABLE presets (
  id SERIAL PRIMARY KEY,
  name VARCHAR(100) UNIQUE NOT NULL,
  description TEXT,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```  

## 4. API Design and Endpoints

We follow a RESTful style, where each URL corresponds to a clear action or resource.

- **Authentication**
  - `POST /api/auth/sign-up` → Create a new user account.
  - `POST /api/auth/sign-in` → Log in and create a session token.
  - `POST /api/auth/sign-out` → Invalidate the session token.

- **Image Workflow**
  - `POST /api/images/upload` → Accepts an image file, stores it temporarily, returns a URL.
  - `POST /api/images/process` → Sends the image URL and preset choice to Chutes.ai, returns the processed image URL.
  - `GET /api/images/history` → Returns a list of past image_records for the logged-in user.

- **Presets Management**
  - `GET /api/presets` → List all available presets.
  - `POST /api/presets` → (Admin only) Add a new preset to the database.

Each endpoint:
- Expects JSON in request bodies.
- Returns JSON with a clear `status` and `data` or `error` field.

## 5. Hosting Solutions

- **Frontend & API**
  - Deployed on Vercel (or similar platforms like Netlify). Vercel automatically builds the Next.js project and serves both the frontend and API routes.
  - Benefits: automatic SSL, global CDN, zero-config scaling.

- **Database**
  - Hosted on a managed PostgreSQL service (AWS RDS, Neon, or Heroku Postgres).
  - Benefits: automated backups, high availability, easy scaling of storage and compute.

- **Docker (Local Development)**
  - A Docker Compose setup runs:
    - The Next.js app on one container.
    - A local Postgres instance on another.
  - This matches production as closely as possible for easier debugging.

## 6. Infrastructure Components

- **Load Balancing & CDN**
  - Vercel’s edge network acts as a CDN and load balancer, routing traffic to the nearest serverless function.

- **Caching**
  - Static assets (JavaScript, CSS, images) are cached at the CDN level.
  - API responses can be cached using HTTP cache headers for endpoints that change infrequently (e.g., `/api/presets`).

- **Logging & Queues**
  - Application logs are captured by Vercel or a logging service (e.g., Logflare).
  - Long-running tasks (if needed) can be offloaded to a background queue (e.g., AWS SQS) and processed by serverless functions.

## 7. Security Measures

- **Authentication & Authorization**
  - User sessions managed by Better Auth using secure, HTTP-only cookies.
  - Protect all private endpoints by checking the session token on each request.

- **Data Encryption**
  - All traffic is served over HTTPS/TLS.
  - Sensitive data at rest (e.g., user passwords, session tokens) is encrypted by the database service.

- **Environment Variables**
  - API keys and database credentials live in Vercel’s environment settings or a `.env` file locally.
  - Never commit secrets to version control.

- **Additional Protections**
  - Input validation on all API routes (limit image sizes and types).
  - Rate limiting to prevent abuse of image processing endpoints.
  - Regular dependency updates to patch security vulnerabilities.

## 8. Monitoring and Maintenance

- **Performance Monitoring**
  - Use Vercel’s built-in analytics to track response times and error rates.
  - Integrate Sentry (or a similar error-tracking tool) to capture exceptions and stack traces.

- **Alerts & Dashboards**
  - Set up alerts for high error rates or slow response times.
  - Maintain a lightweight dashboard showing uptime, request volume, and average latency.

- **Maintenance Routines**
  - Weekly dependency checks and updates (via Dependabot or Renovate).
  - Monthly database maintenance: vacuum, reindex, and review slow queries.
  - Quarterly security reviews, including checking for outdated packages or misconfigurations.

## 9. Conclusion and Overall Backend Summary

The **shootmagic-image-workflow** backend is built on a modern, serverless-friendly stack: Next.js API Routes, TypeScript, Drizzle ORM with PostgreSQL, and Better Auth for secure sessions. It scales effortlessly on Vercel, leverages a managed database service for data reliability, and uses CDN caching for fast delivery worldwide. Clear separation of concerns—API routes, service layer, data layer—ensures the codebase is easy to maintain. Security, monitoring, and maintenance practices are in place to protect user data and keep the system healthy. This setup offers a reliable foundation for any future features, from advanced image processing pipelines to admin dashboards.