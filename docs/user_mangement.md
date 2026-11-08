### 📁 Document 1: `01-user-management.md`

# User & Team Management

## Overview
The Team Management system allows Company Owners and Managers to invite new users to their workspace. Instead of creating passwords for them, the system generates a secure, single-use invitation link. 

Crucially, because this is a multi-plant SaaS, users are not just invited to the "Company"—they are invited to a **Specific Plant**.

## 1. The Invitation Workflow (Admin Side)

1. Navigate to **Settings → Team Members**.
2. Enter the email address of the new user.
3. Select the **Plant** they will be assigned to (e.g., "Chennai Plant").
4. Click **Invite**.
5. The system checks the limit: `Base 2 Users + Extra Seats (Purchased)`.
   * *If the limit is reached, the form is disabled, and the Admin is prompted to upgrade their billing plan.*
6. A unique token is generated and saved to the `Invitation` database table with a 7-day expiration.

## 2. The Signup Workflow (User Side)

1. The invited user clicks the link: `https://app.com/signup?invite=TOKEN`.
2. **Smart UI:** The page recognizes the token. It shows a blue banner: *"You've been invited to join [Company Name] at [Plant Name]"*. The email field is locked to prevent tampering.
3. The user enters their name, password, and verifies their email via a 6-digit OTP.
4. **Database Action:** The system creates the `User`, deletes the `Invitation` token, and creates a `PlantMember` record linking the user to the specific plant.

## 3. Role-Based Access Control (RBAC)

When inviting a user, you assign them a role:
*   **OWNER:** Full access to billing, plants, users, and data.
*   **MANAGER:** Can upload data, manage plants, and invite users. Cannot access billing.
*   **VIEWER:** Read-only access to dashboards.

---

### 📁 Document 2: `02-multi-plant-system.md`

# Multi-Plant (Factory) Management

## Overview
Manufacturing BI Copilot uses a **Multi-Tenant, Multi-Location Architecture**. A Company can own multiple Plants (Factories). All operational data (Production, Inventory, Orders) is strictly isolated by `plantId`.

## 1. How to Add a Plant

1. Navigate to **Settings → Manage Plants**.
2. Enter the Plant Name (e.g., "Pune Unit 2") and a unique Code (e.g., "PUN-02").
3. Click **Add Plant**.
4. **Plan Limits:** The system checks `Company.extraPlants`. If the limit is exceeded, the creation is blocked, and an upgrade prompt appears.
5. Upon creation, the Admin is automatically added as the primary `PlantMember` of the new plant.

## 2. The Plant Switcher (Context Switching)

The Plant Switcher is located in the top navigation bar. 
*   **Security:** When a user selects a plant from the dropdown, a Server Action (`switchPlantAction`) verifies the user actually has a `PlantMember` record for that plant. 
*   **Cookie:** If verified, an HTTP-only cookie (`active_plant_id`) is set.
*   **Middleware:** Next.js middleware intercepts all requests, reads this cookie, and injects it into the request headers (`x-plant-id`).
*   **Data Isolation:** Every Server Component uses `getTenantContext()` which reads the header and ensures database queries are scoped: `where: { plantId: activePlantId }`.

---

### 📁 Document 3: `03-billing-and-payments.md`

# Billing & Subscription Payments

## Overview
The platform uses **Razorpay UPI AutoPay (e-Mandates)** to handle recurring subscriptions. This allows users to scan a QR code once and have their subscription auto-debited every 30 days.

## 1. Pricing Structure
*   **Base Starter Plan:** ₹3,999/month (Includes 1 Plant, 2 Users).
*   **Add-ons:** Extra User Seat (+₹499/mo), Extra Plant (+₹1,499/mo).

## 2. The Payment Flow

1. **Plan Builder:** The user goes to **Settings → Billing**. They select the number of extra seats and plants they need. The UI calculates the total dynamically.
2. **QR Generation:** The backend calls the Razorpay API to create a Subscription plan. The UI displays a dynamic QR code.
3. **Authorization:** The user scans the QR with any UPI app (GPay, PhonePe) and authorizes the monthly mandate.
4. **Webhook Activation:** Razorpay sends a `subscription.activated` webhook to `/api/payment/webhook`.
   *   The server verifies the cryptographic signature.
   *   Updates the `Company` table: `planType = "STARTER"`, `extraSeats = X`, `extraPlants = Y`, `planExpiresAt = +30 days`.
5. **Auto-Renewal:** Every month, Razorpay auto-charges the user and sends a `subscription.charged` webhook to extend the subscription.

## 3. Invoices
*   Users can download GST-compliant tax invoices from the Billing page. 
*   Invoices are generated on-the-fly as PDFs using `jsPDF`, breaking down the base amount and the 18% GST.

---

### 📁 Document 4: `04-data-ingestion.md`

# Data Ingestion & Upload Wizard

## Overview
Users can bulk-import production, inventory, and order data via CSV/Excel files. The system uses a 3-step wizard to ensure data integrity.

## 1. The Upload Wizard

1. **Select File:** Drag and drop a CSV/Excel file.
2. **Map Columns:** The system reads the CSV headers. The user maps their raw headers (e.g., `tx_date`) to the database schema (e.g., `Transaction Date`).
3. **Import:** The file is processed.

## 2. Plant-Scoping During Upload
*   **CRITICAL:** When a file is uploaded, the `/api/upload/ingest` route reads the `active_plant_id` cookie.
*   Every row inserted into the database is stamped with `companyId` AND `plantId`.
*   This ensures that when the user switches to the Chennai Plant, the Pune data does not leak into the dashboard.

## 3. Upload History
*   Below the wizard, users see a table of **Recent Data Imports**.
*   **View Data:** Clicking the Eye icon opens a modal showing the exact rows imported from that specific file (via `/api/upload/data`).
*   **Delete:** Clicking the Trash icon deletes the `DataUpload` record, allowing users to clean up accidental uploads.

---

### 📁 Document 5: `05-factory-calendar.md`

# Factory Calendar & Off-Day Management

## Overview
Factories are not open 24/7. The Factory Calendar prevents the AI from generating false alerts on days the plant is intentionally closed.

## 1. How it Works
1. Go to **Settings → General**.
2. Click **Manage Calendar**.
3. By default, all days are working days. To close a day, click a date, enter a reason (e.g., "Diwali"), and click **Set as Closed**.

## 2. The "Pause" State
When the current date matches a closed date in the database:
*   The Dashboard shows a prominent amber banner: *"🌴 Factory is Closed Today."*
*   **Graphs Freeze:** The dashboard fetches data for the *last working day* instead of showing blank "zero" charts.
*   **AI Pauses:** The nightly cron job that generates AI production plans skips generation.

---

### 📁 Document 6: `06-audit-logbook.md`

# Audit Logbook (Security & Compliance)

## Overview
The Audit Logbook is an immutable chronological record of all critical actions taken within the system (logins, data uploads, billing changes).

## 1. The UI
*   Located at **Logbook → View Audit Trail**.
*   Displays a high-density table with Monospaced timestamps, actor email, action slug (e.g., `data.upload.success`), and IP address.
*   **Status Badges:** Success (Green), Warning (Amber), Error (Red).

## 2. Expandable JSON Payload
*   Clicking any row slides open a drawer on the right side of the screen.
*   It shows the exact JSON payload of the event (e.g., the filename and row count of an upload), making it easy for developers to debug issues.

## 3. How to Log an Event (Developer Guide)
To add a new log, call the Prisma create method inside your API route:
```ts
await prisma.auditLog.create({
  data: {
    companyId,
    actor: user.email,
    action: "settings.update",
    ipAddress: req.headers.get("x-forwarded-for") || "127.0.0.1",
    status: "SUCCESS",
    payload: { updatedField: "company_name" },
  }
});
```
