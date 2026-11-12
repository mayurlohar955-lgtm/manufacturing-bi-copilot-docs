# Production Forecasting Guide

## Overview
The Forecasting engine uses Machine Learning (Time-Series forecasting) to predict future production output, machine failures, and inventory depletion. This helps plant managers shift from **reactive** firefighting to **proactive** planning.

## 1. Production Forecasting

### How it works:
1. The AI analyzes the last 90 days of production logs.
2. It accounts for day-of-week seasonality (e.g., Monday shifts are always slower).
3. It generates a **7-day forecast** of expected production output per line.

### Where to see it:
*   **Dashboard -> Trend Charts:** The dotted line extending beyond today's date shows the predicted output.
*   **AI Insights:** If the predicted output is lower than the target, the AI will suggest shifting resources to that line.

## 2. Predictive Maintenance (Machine Failure)

### How it works:
The ML model monitors machine-level downtime and scrap rates. It looks for "creeping" degradation:
*   A machine that has 10 minutes of downtime every day for 5 days is more likely to fail soon than one with a single 50-minute failure.
*   The AI triggers a `WARNING` alert: *"Press #4 has shown increasing vibration/downtime over 5 days. Schedule maintenance."*

## 3. Demand vs. Capacity Forecasting

The system compares incoming orders (Demand) against your historical production capacity (Capacity).
*   If the forecast shows you will produce 1,000 units next week, but you have 2,000 units of orders due, the AI will trigger a **Capacity Alert** on the Dashboard.

## 4. Model Limitations

*   **Data Quality:** Forecasts are only as good as the data. If downtime is not logged correctly, the model cannot predict it.
*   **Black Swan Events:** The model cannot predict sudden power grid failures or natural disasters; it only predicts based on historical patterns.
*   **Cold Start:** A new factory needs at least 14 days of data before the forecasting engine generates reliable predictions.
