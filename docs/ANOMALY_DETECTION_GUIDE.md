# Automatic Insights & Anomaly Detection Guide

Manual spreadsheet reviews often miss subtle operational issues until they turn into costly batch rejections or machine stoppages. Manufacturing BI Copilot automatically scans your data to highlight unusual patterns and trigger real-time alerts.

---

## 1. What Types of Anomalies Are Detected?

The platform continuously checks your uploaded logs and connected databases for three primary types of operational anomalies:

* **Telemetry Spikes & Drops:** Sudden jumps in spindle temperature, motor current, hydraulic pressure, or cycle times compared to a machine's 30-day baseline.
* **Quality & Scrap Surges:** Unusual increases in `defect_count` or rejection percentages during a specific shift, operator rotation, or raw material batch.
* **Output Bottlenecks:** Unexplained drops in hourly `units_produced` even when no formal downtime error code was logged.

---

## 2. How Automatic Insights Work

Whenever you upload a new Excel or CSV file, the platform runs an automated diagnostic scan before you even type a question:

1. **Baseline Calculation:** The system calculates normal operating ranges for every machine and shift in your file.
2. **Outlier Flagging:** Any data point falling outside standard statistical limits is flagged as an anomaly.
3. **Root-Cause Correlation:** The AI checks if the anomaly happened alongside another event (for example, *"Scrap rate spiked by 18% whenever Oven 2 exceeded 215°C"*).

---

## 3. Reviewing and Investigating Alerts

You can inspect flagged issues directly from the **Alerts** dashboard or through the AI Assistant:

1. Open the **Alerts** tab in your [Manufacturing BI Copilot](https://bhavu.co) workspace.
2. Filter alerts by **Severity** (*High*, *Medium*, *Low*), **Machine ID**, or **Date Range**.
3. Click on any alert card to view the exact timestamp, affected metric, and surrounding sensor readings.
4. Click **"Ask AI About This Alert"** to generate a plain-English summary of potential causes and recommended corrective actions.

### Sample Investigation Prompts
* *"List all high-severity anomalies detected on CNC-04 over the past 7 days."*
* *"Why did the rejection rate spike during yesterday's Night Shift on Assembly Line 2?"*
* *"Compare yesterday's anomalies against our maintenance logs from last week."*

!!! note "Reducing False Alarms"
    If a specific machine normally runs hotter during heavy-duty jobs, include a `product_sku` or `job_type` column in your dataset so the AI learns separate baselines for each product type.
