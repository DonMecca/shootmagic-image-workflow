# shootmagic-image-workflow: Security Guidelines

## 1. Introduction
This document outlines security best practices and requirements for the client-side image workflow application built with React (Vite) and the Chutes.ai API. Adhering to these guidelines will help ensure that the application remains robust, protects user data, and resists common web threats.

## 2. Core Security Principles (Applied)

- Security by Design: Embed security into every feature—file uploads, API calls, and storage—rather than bolting it on later.
- Least Privilege: Only request and store the minimum data you need. Limit localStorage usage strictly to processed image metadata.
- Defense in Depth: Combine input validation, content security policies, and error handling to provide multiple protection layers.
- Fail Securely: On errors (e.g., network failures, malformed inputs), show generic messages without revealing stack traces or sensitive details.
- Secure Defaults: Ship with strict Content Security Policies (CSP) and CORS rules, and require HTTPS by default.

## 3. Threat Models & Mitigations

### 3.1 File Upload & Input Validation
- Validate file types and sizes client-side before sending to Chutes.ai: only allow JPEG, PNG, ≤ 10 MB.
- Reject unexpected MIME types and scan Base64 payloads for malformed data.
- Use HTML5 drag-and-drop APIs safely—sanitize filenames to prevent path traversal or script injection.

### 3.2 Local Storage Security
- Store only non-sensitive data (image URLs, preset selections, timestamps). Avoid PII or raw image blobs.
- Namespace keys (e.g., `shootmagic:results`) to avoid collisions.
- Implement a size cap and eviction strategy: remove the oldest entries when exceeding storage quota.
- On page load, validate stored entries against a schema to guard against corruption or tampering.

### 3.3 API Integration & Key Management
- Keep the Chutes.ai key out of client code. Use a build-time environment variable (`import.meta.env.VITE_CHUTES_API_KEY`) injected by Vite.
- Never commit `.env` or production keys to source control. Provide a `.env.example` without real secrets.
- For increased security in production, consider a lightweight serverless proxy that stores the key server-side and forwards requests.
- Enforce HTTPS on all API calls; reject insecure HTTP endpoints.
- Implement exponential back-off and rate-limiting client-side to reduce risk of brute-forcing or flooding the Chutes.ai endpoint.

### 3.4 Cross-Site Scripting (XSS) & Injection
- Sanitize and encode all dynamic content (filenames, error messages, URLs) before rendering in React.
- Use React’s built-in escaping by avoiding `dangerouslySetInnerHTML`.
- Apply a strict CSP header via your static host (e.g., `Content-Security-Policy: default-src 'self'; img-src 'self' https://chutes.ai; script-src 'self'`).

### 3.5 Cross-Site Request Forgery (CSRF)
- As a purely client-side app, CSRF risk is minimal. If you implement a serverless proxy later, include anti-CSRF tokens in requests.

### 3.6 CORS Configuration
- Configure your proxy or static host to allow only your application’s origin to fetch resources.
- Avoid wildcard (`*`) in `Access-Control-Allow-Origin` for production.

### 3.7 Error Handling & Information Leakage
- Catch and handle network or API errors gracefully. Show user-friendly alerts like “Image processing failed. Please try again.”
- Log detailed errors only in development. Strip stack traces or internal URLs in production builds.

### 3.8 Dependency Management
- Vet all NPM packages before inclusion. Prefer libraries with active maintenance and no known high-severity CVEs.
- Lock dependencies via `package-lock.json` and run automated SCA (Software Composition Analysis) in CI.
- Regularly update Vite, React, and shadcn/ui to patch vulnerabilities.

## 4. Testing & CI/CD Security
- Write unit tests with Vitest for the storage utility and API module. Mock fetch calls to simulate errors and success paths.
- Include security linting (ESLint plugins like `eslint-plugin-security`) in the CI pipeline.
- Run automated vulnerability scans on each pull request (e.g., `npm audit`, Snyk, or Dependabot alerts).
- Ensure builds fail on high-severity vulnerabilities or linting errors.

## 5. Deployment Recommendations
- Serve over HTTPS with HSTS (`Strict-Transport-Security: max-age=63072000; includeSubDomains; preload`).
- Use a minimal static file host (e.g., Netlify, Vercel) and enable secure headers (CSP, HSTS, X-Frame-Options).
- Disable directory listings and any debug endpoints in production.

## 6. Summary of Security Controls

| Area                               | Controls                                                                                 |
|------------------------------------|------------------------------------------------------------------------------------------|
| File Upload                        | Type/size validation, sanitized filenames, queued processing schema                      |
| Local Storage                      | Namespaced, schema-validated entries, size cap & eviction                               |
| API Key Management                 | Env variables, no commits, optional proxy, HTTPS-only                                   |
| XSS & Injection                    | React escaping, no `innerHTML`, strict CSP                                              |
| CORS & Headers                     | Restrictive origins, HSTS, X-Frame-Options, X-Content-Type-Options                       |
| Error Handling                     | User-friendly messages, no stack traces in prod, dev-only logging                        |
| Dependencies & CI/CD               | Lockfiles, SCA scans, security linting, fail on high-severity issues                      |

---

By following these guidelines, the shootmagic-image-workflow application will maintain a strong security posture, protect user data, and minimize the risk of compromise throughout its development and deployment lifecycle.