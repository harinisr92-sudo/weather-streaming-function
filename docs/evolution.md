# Evolution of the Weather Streaming Pipeline

This document explains the full engineering journey of the Weather Streaming Pipeline, from Databricks prototypes to Azure Function ingestion and finally Microsoft Fabric real-time analytics.

## 1. Databricks Prototype (Initial Version)

The project began as a Databricks-based streaming pipeline.

**What was built**

- Python notebook calling WeatherAPI.
- JSON flattening logic.
- Event Hubs producer using `azure-eventhub`.
- Spark Structured Streaming job using a `rate` source as a timer.
- Checkpoints stored in a Unity Catalog volume.

**Why it worked**

- Easy to prototype.
- Spark handled streaming logic.
- Unity Catalog provided secure checkpoint storage.

**Why it was replaced**

- Databricks clusters are expensive for frequent API calls.
- The `rate` source is a workaround, not a true scheduler.
- NAT Gateway costs increased unexpectedly.
- A lightweight serverless ingestion layer was preferred.

## 2. Azure Function Attempt 1 — Python (Windows Consumption Plan)

**What was tried**

- Python timer-triggered Azure Function.
- WeatherAPI ingestion and JSON flattening.
- Event Hubs publishing.

**Why it failed**

- Windows Consumption Plan has limited Python support.
- The Python worker failed to start.
- Dependencies could not install.

**Outcome:** This hosting plan was not suitable for Python ingestion.

## 3. Azure Function Attempt 2 — PowerShell (Same Function App)

**What was tried**

- Switched to the PowerShell runtime.
- Implemented WeatherAPI calls.
- Added Event Hubs producer logic.

**Why it failed**

- Missing `requirements.psd1` file.
- The worker could not load modules.
- Event Hub client initialization errors.

**Outcome:** PowerShell worked better, but the Function App configuration was unstable.

## 4. Azure Function Attempt 3 — Python (Flex Consumption Plan, Linux)

**What was built**

- New Function App: `fn-weather-streaming`.
- Python timer trigger.
- WeatherAPI ingestion and JSON flattening.
- Event Hubs publishing.
- Integration with Fabric Eventstream.

**Why it succeeded**

- Flex Consumption supports Python reliably.
- Dependencies installed cleanly.
- The timer trigger executed every 30 seconds.
- Events flowed consistently into Fabric.

**Outcome:** This became the final ingestion layer.

## 5. Final Architecture

```
WeatherAPI
    ↓
Azure Function App (Python, Flex Consumption)
    ↓
Azure Event Hubs
    ↓
Microsoft Fabric Eventstream
    ↓
Fabric Eventhouse (KQL)
    ↓
Power BI Real-Time Dashboard
```

## 6. Key Engineering Learnings

- Databricks is powerful but costly for lightweight streaming.
- Flex Consumption is ideal for Python-based ingestion.
- Managed Identity improves security over connection strings.
- Fabric Eventstream + Eventhouse provide true real-time analytics.
- Power BI and Activator complete the alerting loop.

## 7. Summary

The pipeline evolved from a Spark-based prototype to a serverless, real-time architecture using Azure Functions and Microsoft Fabric. This journey demonstrates practical cloud engineering, debugging and architectural decision-making.
