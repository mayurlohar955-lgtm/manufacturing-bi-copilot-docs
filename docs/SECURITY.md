# Enterprise Security Architecture & Multi-Tenancy

**Product:** Manufacturing BI Copilot  
**Document:** Security Architecture and Data Isolation  
**Last updated:** October 29, 2026

## 1. Multi-Tenant Architecture & Data Isolation

Manufacturing BI Copilot is designed to support multiple manufacturing companies within a shared application while keeping their business data logically separated.

### Company-Level Data Isolation

Core operational data includes the following entities:

- `ProductionLog`
- `DowntimeLog`
- `InventoryItem`
- `ScrapLog`
- `Order`
- `DataUpload`

The application uses a `companyId` tenant identifier to associate business records with the relevant company. The database schema and application logic should enforce this association consistently.

### Server-Side Tenant Resolution

The application's tenant-resolution logic is implemented in `src/lib/tenant.ts`.

The `getCurrentCompanyId()` function is intended to derive the active company context from the authenticated server-side session. Business-data operations should use this trusted context rather than accepting an unrestricted company identifier from a client request.

This approach helps prevent users from accessing records belonging to other companies.

**Security requirement:** All relevant database queries, API endpoints, background operations, and file-access paths must enforce company-level authorization. Tenant isolation must not depend solely on the user interface.

### Database Indexing

Composite indexes that include `companyId` can improve the performance of company-scoped queries.

Indexing supports efficient data retrieval, but it is not itself an authorization mechanism. Access control must be enforced separately through the application's query logic and database permissions.

The actual indexes should be confirmed against the Prisma schema and deployed database migrations.

## 2. Authentication & Session Security

Manufacturing BI Copilot uses a custom authentication implementation located in `src/lib/auth/`.

### Password Hashing

The application uses `bcryptjs` for password hashing.

Passwords should be hashed before storage and verified through the server-side authentication process. Plaintext passwords should never be stored in the database or written to application logs.

### Session Token Protection

The reported session implementation uses randomly generated session tokens and stores their SHA-256 hashes in the database.

This design reduces the risk of session-token disclosure if an attacker obtains only the stored token hashes. Its effectiveness also depends on token generation, session validation, expiration, invalidation, and protection of active tokens.

### Cookie Security

The reported authentication implementation uses HTTP-only cookies and `SameSite=Lax`.

For production deployments, session cookies should also use the `Secure` attribute so they are transmitted only over HTTPS.

The deployed cookie configuration should be verified to ensure these protections are applied consistently.

HTTP-only cookies prevent ordinary client-side JavaScript from reading the cookie directly. They do not eliminate every risk associated with cross-site scripting, cross-site request forgery, or compromised user sessions.

### Route and API Protection

The application uses `middleware.ts` and server-side authentication checks to protect application routes and API endpoints.

Protected resources should reject unauthenticated requests and verify authorization before returning or modifying sensitive data.

Authentication and authorization should be tested separately. A user being logged in does not automatically mean that the user is authorized to access every resource.

## 3. Guarded Generative AI Operations

Manufacturing BI Copilot integrates Anthropic Claude, using the `claude-sonnet-4-6` model identifier for the relevant AI functionality.

AI-assisted analysis introduces additional data-protection considerations because prompts, database results, and other contextual information may be transmitted to an external AI service.

### Server-Side AI Processing

AI requests should be initiated through controlled server-side application logic.

API credentials must remain on the server and must not be exposed in client-side code, public repositories, or browser responses.

The information sent to the AI provider should be limited to what is necessary for the requested operation.

### Controlled Metric Calculations

The application uses `src/lib/metrics.ts` as a centralized location for defined manufacturing metrics.

Code-defined calculations can help ensure consistent interpretation of measures such as production performance, scrap rate, downtime, and inventory levels.

AI-generated explanations should be distinguished from calculations performed by application code. The application should validate AI-generated queries and enforce authorization before executing database operations.

### AI Query and Data-Access Boundaries

AI-generated output should not automatically be trusted as safe database instructions.

Appropriate safeguards include:

- Restricting queries to authorized company data.
- Validating generated queries before execution.
- Limiting database permissions to the operations required.
- Preventing unauthorized data modifications.
- Avoiding the transmission of unnecessary confidential information.
- Testing for attempts to bypass tenant restrictions.

These controls should be described as implemented only to the extent that the deployed code demonstrates them.

### Customer Data and Model Training

Manufacturing BI Copilot uses Anthropic's API for AI-powered functionality.

The treatment of information transmitted through that API is governed by the applicable Anthropic service terms, privacy policies, and account configuration.

**Do not publish an unconditional guarantee that customer data is never used for model training or retained unless the applicable terms and configuration confirm it.** Verify the policy that applies to your specific API usage before making a definitive commitment to customers.

---

## 4. Security Principles

Manufacturing BI Copilot's security architecture should follow these principles:

| Principle | Application |
|---|---|
| Least privilege | Users and services should receive only the permissions they need. |
| Server-side authorization | Sensitive data access must be authorized on the server. |
| Tenant isolation | Business records must be scoped to the authenticated user's company. |
| Secure session management | Authentication tokens and cookies must be protected throughout their lifecycle. |
| Data minimization | Only necessary data should be transmitted to external AI services. |
| Input validation | Uploaded files and generated queries must be validated before processing or execution. |
| Defense in depth | Security should rely on multiple complementary controls rather than a single mechanism. |
| Continuous verification | Security controls should be tested after relevant code and configuration changes. |

## 5. Scope and Verification

This document describes the intended architecture and reported implementation of Manufacturing BI Copilot. It is not an independent security audit or certification.

The following controls should be verified against the deployed application before being presented as confirmed security guarantees:

- Every business-data query applies the correct company scope.
- Direct API requests cannot access another company's records.
- Uploaded files cannot be accessed by unauthorized users.
- Production cookies use the required security attributes.
- Sessions expire and can be invalidated appropriately.
- AI-generated database operations are validated and restricted.
- The applicable Anthropic API terms support any published data-retention or model-training commitments.
- Database indexes and constraints match the deployed Prisma schema.

**Security objective:** Protect manufacturing business information through consistent authorization, tenant isolation, secure authentication, controlled AI processing, and ongoing verification.

---
