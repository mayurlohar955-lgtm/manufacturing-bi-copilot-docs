# Troubleshooting Guide

Use this guide to quickly resolve common errors when uploading data or running calculations on the platform.

## 1. Data Upload Failures

### Error: "Invalid File Format or Schema"
This happens if your file structure is corrupted or missing proper headers.
*   **The Fix:** Make sure the very first row of your Excel or CSV sheet contains clear column headers (e.g., `timestamp`, `machine_id`, `temperature`). Remove blank rows at the top of the file before uploading.

### Error: "Upload Timeout"
This happens if your internet connection drops or the file exceeds size guidelines.
*   **The Fix:** Compress your file, or split large sheets into smaller batches (e.g., one sheet per week instead of a whole year).

## 2. AI Assistant Issues

### Error: "I cannot find the relevant columns in your data"
The AI assistant displays this if it cannot understand your question based on your column names.
*   **The Fix:** Be more specific in your query. Instead of asking *"Show me problems,"* try asking *"Show me rows where temperature is greater than 80"*. Ensure your headers use standard technical words.

## 3. Database Sync Failures

### Error: "Connection Refused by Peer"
The platform cannot reach your local database server.
*   **The Fix:** Check your firewall rules. Ensure that the Manufacturing BI Copilot IP addresses are explicitly whitelisted in your cloud provider or network settings.

!!! danger "Security Warning"
    Never paste your raw database password strings or access tokens into public GitHub tickets or support chat strings when asking for troubleshooting help.
