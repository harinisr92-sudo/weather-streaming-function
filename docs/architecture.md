# Real-Time Weather Streaming Pipeline — Architecture

This document explains the end-to-end architecture of the Weather Streaming Pipeline, covering Databricks, Azure Functions, Event Hubs, Microsoft Fabric, and Power BI.

## 1. High-Level Architecture

```
WeatherAPI
    ↓
Azure Function App (Python, Flex Consumption)
    ↓
Azure Event Hubs
    ↓
Microsoft Fabric Eventstream
    ↓
Fabric Eventhouse (KQL Database)
    ↓
Power BI Real-Time Dashboard
```

## 2. Components

### WeatherAPI (REST)

- Provides current weather, air quality, forecast, and alert data.
- Queried every 30 seconds.

### Azure Function App (Python, Flex Consumption Plan)

- Timer-triggered ingestion layer.
- Calls WeatherAPI, flattens JSON, and publishes events to Event Hubs.
- Uses `requirements.txt` for dependency management.
- Runs on Linux Flex Consumption for reliable Python support.

### Azure Event Hubs

- Acts as the streaming ingestion buffer.
- Receives JSON events from the Function App.
- Integrates natively with Microsoft Fabric Eventstream.

### Microsoft Fabric Eventstream

- Real-time router.
- Pulls events from Event Hubs.
- Pushes them into Eventhouse tables.

### Fabric Eventhouse (KQL Database)

- Stores streaming data.
- Supports time-series queries, filtering, projections, and alert extraction.
- Used by Power BI and Activator.

### Power BI

- Real-time dashboard built on Eventhouse.
- Displays temperature, humidity, wind speed, city trends, and alerts.

### Fabric Activator

- Monitors alert fields.
- Triggers notifications when WeatherAPI reports severe conditions.

## 3. Databricks Prototype (Earlier Version)

Before Azure Functions, the pipeline used Databricks notebooks:

- Python producer notebook calling WeatherAPI.
- Spark Structured Streaming with a `rate` source as a timer.
- Checkpoints stored in a Unity Catalog volume.
- Events published using `azure-eventhub`.

This version worked but was replaced due to cost and scheduling limitations.

## 4. Final Architecture Decision

Azure Function (Flex Consumption) was chosen because:

- True timer trigger (no Spark workaround).
- Lower cost than Databricks.
- Native Python support.
- Seamless integration with Event Hubs and Fabric.
