# Weather Streaming Function

## Overview
This Azure Function streams live weather data from WeatherAPI to Azure Event Hub, which feeds Microsoft Fabric Eventstream → Eventhouse → Power BI for real‑time dashboards.

## Architecture
WeatherAPI → Azure Function (Timer Trigger) → Event Hub → Fabric Eventstream → Eventhouse → Power BI

## Key Files
- `function_app.py` — main logic
- `host.json` — function host configuration
- `local.settings.json` — local environment (excluded from Git)
- `requirements.txt` — dependencies

## Deployment
```bash
func azure functionapp publish fp-weather-streaming
