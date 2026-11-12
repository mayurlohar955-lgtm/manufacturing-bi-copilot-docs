# Anomaly Detection & Alerting Guide

## Overview
Manufacturing processes are highly variable, but certain deviations indicate real problems (machine failure, material defects, or data entry errors). The Anomaly Detection engine continuously monitors production data and automatically flags unusual patterns before they cause significant financial loss.

## 1. How Anomalies are Detected

The system uses a **Statistical Process Control (SPC)** approach combined with Machine Learning.

1. **Baseline Calculation:** For every production line, the system calculates the rolling 7-day average for OEE, Scrap Rate, and Downtime.
2. **Threshold Limits:** It establishes Upper and Lower Control Limits (UCL/LCL) based on historical variance.
3. **Triggering:** If a new data point falls outside these limits, an `Alert` is generated.

### Types of Anomalies Detected:
*   **Scrap Spike:** Scrap rate jumps significantly (e.g., from 2% to 8%) on a specific line.
*   **Downtime Event:** A machine experiences consecutive micro-stoppages that exceed the daily average.
*   **Production Drop:** Actual output falls below 70% of the target for the shift.
*   **Inventory Discrepancy:** Stock movements show a discrepancy between expected and actual inventory.

## 2. Alert Lifecycle

1. **Triggered:** An anomaly is detected. An `Alert` record is created with `status: OPEN`.
2. **Notification:** The alert appears on the Dashboard (`Active Alerts` card) and the `/alerts` page.
3. **Acknowledgment:** A manager clicks "Acknowledge" → `status: ACKNOWLEDGED` (stops duplicate notifications).
4. **Resolution:** Once the root cause is fixed, the manager clicks "Resolve" → `status: RESOLVED`.

## 3. Severity Levels

*   🔴 **CRITICAL:** Immediate action required (e.g., Machine failure, OEE drops below 40%).
*   🟡 **WARNING:** Investigate soon (e.g., Scrap rate creeping up, minor downtime).
*   ⚪ **INFO:** Informational only (e.g., Shift started late).

## 4. Suppressing False Alerts (Off-Days)

The anomaly engine automatically respects the **Factory Calendar**:
*   If the factory is marked as "Closed" on a specific date, the anomaly engine pauses.
*   No alerts will be triggered for zero production on weekends or planned shutdowns.
