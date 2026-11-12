# ERP & SQL Database Integration Guide

## Overview
While Manufacturing BI Copilot supports manual CSV/Excel uploads, enterprise factories require real-time synchronization with their existing systems (SAP, Oracle, Tally, or custom SQL databases). This guide explains how to connect your ERP or SQL database to the Copilot.

## 1. Supported Integration Methods

### A. Automated CSV/Excel Sync
*   **How it works:** An external script (e.g., Python, PowerShell) runs on your ERP server, exports the daily production data to a CSV, and uploads it to the Copilot API.
*   **Endpoint:** `POST /api/upload/ingest`
*   **Best for:** Legacy ERPs without direct database access.

### B. Direct SQL Connection (PostgreSQL/MySQL)
*   **How it works:** The Copilot connects directly to a read-only replica of your ERP database and runs scheduled queries to fetch production logs.
*   **Setup:** Provide the Database URL, Username, and Password in the `Settings -> Integrations` page.
*   **Security:** The connection requires read-only permissions. The Copilot never modifies your ERP data.

### C. REST API Webhooks
*   **How it works:** If your ERP supports webhooks (e.g., when an order is fulfilled or a machine cycle completes), it can send a `POST` request to the Copilot API with the JSON payload.
*   **Endpoint:** `POST /api/v1/production-logs`

## 2. Data Mapping & Schema

When connecting an external system, you must map your ERP columns to the Copilot schema.

| ERP Field | Copilot Field | Data Type |
| :--- | :--- | :--- |
| `WO_ID` | `externalId` (Orders) | String |
| `MACHINE_CODE` | `line` (ProductionLogs) | String |
| `GOOD_QTY` | `quantityProduced` | Integer |
| `TARGET_QTY` | `quantityTarget` | Integer |
| `REJECT_QTY` | `scrapQty` | Integer |
| `RUN_DATE` | `date` | ISO-8601 (YYYY-MM-DD) |

## 3. Error Handling & Retries

*   If the ERP API is down, the Copilot will not crash. It will log an error to the `AuditLog` (`integration.sync.failed`).
*   The system retries the sync automatically every 15 minutes for up to 2 hours.
*   Duplicate entries are automatically skipped using the `externalId` unique constraint.
