# Frequently Asked Questions (FAQ)

Find answers to common questions about using the Manufacturing BI Copilot platform.

### General Questions

#### What file formats are supported for data uploads?
The platform officially supports **Excel (`.xlsx`)** and **CSV (`.csv`)** files containing production logs, machine metrics, or inventory records.

#### How big can my upload file be?
!!! note "File Size Limit"
    For optimal AI performance, try to keep your upload files under **50MB**. If your data is larger, consider breaking it into monthly logs or connecting your database directly.

#### Can I connect my live factory database?
Yes. The platform supports secure, direct integrations with major ERP systems and SQL databases (including PostgreSQL, SQL Server). Please see the integration guide for step-by-step connection rules.

### AI Assistant & Analytics

#### How does the AI Assistant analyze my data?
The AI Assistant reads the column headers and data types of your uploaded file. When you ask a question in plain English, it dynamically generates calculations to find patterns, averages, and extreme data values.

#### How accurate are the machine failure predictions?
Predictions are based on mathematical anomalies and historical performance trends in your dataset. 
!!! warning "Operational Requirement"
    AI-generated predictions are designed to flag potential risks early. They should always be reviewed by a qualified maintenance engineer before any real physical changes are made to factory machinery.

#### Is my company's data safe?
Yes. Your uploaded files are processed inside a private environment. Data is used solely to generate your active dashboard charts and is never shared or used to train public AI models.
