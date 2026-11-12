# Production Forecasting & Machine Failure Prediction Guide

Manufacturing BI Copilot uses historical operational records to forecast future production output and identify early warning signs of equipment failure. This guide explains how to prepare your data and interpret AI predictions.

---

## 1. Data Requirements for Accurate Predictions

To generate reliable forecasts, your dataset should meet these minimum guidelines:

* **Historical Depth:** Upload at least **30 days** of continuous daily or hourly production logs. More history improves seasonal and shift-level accuracy.
* **Consistent Time Intervals:** Ensure your `timestamp` column follows a uniform schedule (e.g., hourly sensor readings or end-of-shift summaries) without large missing gaps.
* **Key Telemetry Columns:** Include metrics such as `units_produced`, `cycle_time`, `temperature_c`, `vibration_mm_s`, or `downtime_minutes`.

!!! tip "Improving Forecast Accuracy"
    Remove test runs, scheduled maintenance windows, and holiday shutdown days from your training file so the AI does not mistake planned downtime for a machine breakdown.

---

## 2. Production Output Forecasting

Production forecasting helps plant managers plan raw material orders, shift staffing, and customer delivery timelines.

### How to Run a Production Forecast
1. Open your workspace in [Manufacturing BI Copilot](https://bhavu.co).
2. Upload your historical production log or connect your live SQL view.
3. Navigate to the **Forecasting** tab (or ask the AI Assistant directly).
4. Select your target metric (e.g., `units_produced`) and choose a forecast horizon (**7 days**, **14 days**, or **30 days**).

### Sample AI Forecasting Prompts
* *"Forecast total unit output for Line 1 over the next 14 days based on last month's shift averages."*
* *"Predict when our current stock of Aluminum 6061 rods will run out at our current daily production rate."*

---

## 3. Machine Failure Prediction & Risk Scores

Instead of waiting for a sudden breakdown, the platform monitors gradual drift in machine operating parameters to flag maintenance risks early.

### Understanding Risk Levels
The system evaluates equipment health and assigns a clear risk status:

| Risk Level | Risk Score | What It Means | Recommended Action |
| :--- | :--- | :--- | :--- |
| **Normal** | `0% – 39%` | Machine telemetry is within standard operating limits. | Continue normal production schedule. |
| **Elevated** | `40% – 69%` | Minor drift detected (e.g., rising temperature or slower cycle times). | Inspect during the next scheduled shift changeover. |
| **Critical** | `70% – 100%` | High probability of component wear or impending failure. | Schedule immediate preventive maintenance check. |

!!! warning "Engineering Review Required"
    Machine failure predictions are statistical risk indicators based on historical data patterns. Always have a qualified maintenance engineer physically inspect the equipment before shutting down a production line.
