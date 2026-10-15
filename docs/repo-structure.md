# Repository Structure Overview

This document provides an overview of the folder structure for the Real-Time Weather Streaming Pipeline project. It reflects the evolution of the system, from Databricks prototypes to Azure Function ingestion and Microsoft Fabric analytics.

## 1. Folder Structure

```
weather-streaming-pipeline/
│
├── Databricks/
│   ├── Library installation.ipynb
│   ├── Unity Catalog volume for streaming checkpoints.ipynb
│   ├── weather-streaming-notebook.ipynb
│   └── README.md
│
├── AzureFunction/
│   ├── fp-weather-streaming/        # Windows Consumption attempts (Python & PowerShell)
│   ├── fn-weather-streaming/        # Final Flex Consumption Python Function
│   ├── requirements.txt
│   ├── function_app.py
│   └── README.md
│
├── Fabric/
│   ├── eventstream.json
│   ├── eventhouse.kql
│   ├── powerbi-dashboard.pbix
│   └── README.md
│
└── docs/
    ├── architecture.md
    ├── evolution.md
    ├── troubleshooting.md
    └── repo-structure.md
```

## 2. Folder Descriptions

### Databricks/

Contains the original Spark-based streaming prototype.

- WeatherAPI ingestion and JSON flattening.
- Event Hubs producer using `azure-eventhub`.
- Structured Streaming job using a `rate` source.
- Unity Catalog checkpoint setup.

### AzureFunction/

Contains all Azure Function ingestion attempts.

- `fp-weather-streaming`: Windows Consumption attempts (Python and PowerShell).
- `fn-weather-streaming`: final working Flex Consumption Python Function.
- `requirements.txt`: Python dependencies.
- `function_app.py`: main ingestion logic.

### Fabric/

Contains real-time analytics and visualization components.

- Eventstream routing configuration.
- Eventhouse KQL queries.
- Power BI dashboard.

### docs/

Contains all project documentation.

- `architecture.md`: end-to-end pipeline architecture.
- `evolution.md`: the full engineering journey.
- `troubleshooting.md`: common issues and fixes.
- `repo-structure.md`: this file.

## 3. Summary

This structure makes the project easy to navigate and shows the evolution from Databricks to Azure Functions and then to Microsoft Fabric. It is designed for clarity, maintainability and a professional presentation on GitHub.
