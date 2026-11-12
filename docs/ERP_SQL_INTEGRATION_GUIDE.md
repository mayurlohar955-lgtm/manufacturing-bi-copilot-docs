# ERP & SQL Database Integration Guide

Manufacturing BI Copilot connects directly with your existing factory databases and Enterprise Resource Planning (ERP) systems. This allows you to analyze live production records without manually exporting spreadsheets every day.

---

## 1. Supported SQL Databases

You can establish a secure, read-only connection to the following database engines:

* **Neon PostgreSQL**

## 2. Recommended Database Table Schema

For the AI Assistant to accurately answer questions about downtime, output, and quality, ensure your connected SQL view or table includes clean column names:

```sql
CREATE VIEW production_summary_view AS
SELECT 
    log_timestamp AS timestamp,
    machine_code AS machine_id,
    shift_name AS shift,
    good_units AS units_produced,
    rejected_units AS defect_count,
    downtime_mins AS downtime_minutes,
    spindle_temp AS temperature_c
FROM factory_floor_logs;
```

---

## 3. Exporting Data from Popular ERPs

If your factory uses an on-premise ERP without direct cloud database access, you can schedule automated CSV or Excel exports from your system and upload them into the platform.

### SAP (S/4HANA & Business One)
1. Open your production order report (e.g., transaction **`COOIS`** for Production Order Information System).
2. Filter by your desired plant code and date range.
3. Click **List** -> **Export** -> **Spreadsheet** (`.xlsx`).
4. Upload the exported file directly into the **Upload Data** tab in Manufacturing BI Copilot.

### Odoo Manufacturing (MRP)
1. Go to the **Manufacturing** app -> **Reporting** -> **Work Orders**.
2. Switch to the **List View** and select the records you want to analyze.
3. Click **Action** -> **Export**, select **CSV** or **XLSX**, and include fields for *Work Center*, *Duration*, *Quantity Produced*, and *Status*.

### TallyPrime (Inventory & Raw Material Logs)
1. Open **Gateway of Tally** -> **Display More Reports** -> **Inventory Books** -> **Stock Summary**.
2. Press **`Alt + E`** (Export) and set the file format to **Excel (Spreadsheet)**.
3. Upload the sheet to ask the AI Assistant questions about stock consumption and reorder risks.

!!! tip "Need Custom ERP Assistance?"
    If your plant runs a custom legacy ERP or MES system, [chat with our engineering team on WhatsApp](https://bhavu.co) for help building a custom connector.
