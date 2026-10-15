# Troubleshooting Guide — Weather Streaming Pipeline

This guide documents common issues encountered during development and how they were resolved.

## 1. Azure Function Issues

### Problem: Python worker failed to start (Windows Consumption Plan)

**Symptoms**

- Function runtime crashes.
- Errors like:
  - "Worker failed to load function"
  - "Python worker not found"

**Cause:** Windows Consumption Plan has limited Python support.

**Fix:** Use Flex Consumption (Linux) for Python Functions.

### Problem: PowerShell worker errors in fp-weather-streaming

**Symptoms**

- Missing `requirements.psd1`
- Worker unable to load modules
- Event Hub client initialization failures

**Cause:** PowerShell requires a root-level `requirements.psd1` file.

**Fix:** Switch to Python on Flex Consumption.

### Problem: Event Hub connection failures

**Symptoms**

- "Unauthorized"
- "Cannot open connection"
- "Key not found"

**Cause:** Incorrect Key Vault permissions or missing secrets.

**Fix**

- Verify Key Vault RBAC.
- Ensure the Function App identity has `get` permission.
- Store the Event Hub connection string securely.

## 2. Databricks Issues

### Problem: Secret retrieval failures

**Symptoms**

- `dbutils.secrets.get` throws errors.
- "Permission denied" or "Secret not found."

**Cause:** Key Vault RBAC propagation delays or incorrect Access Connector configuration.

**Fix**

- Assign the `Key Vault Secrets User` role.
- Wait 5–10 minutes for RBAC propagation.
- Validate Access Connector permissions.

### Problem: Streaming checkpoint errors

**Symptoms**

- Spark cannot write checkpoints.
- "Path does not exist" or "Permission denied."

**Cause:** Unity Catalog volume not created or incorrect path.

**Fix:** Run the notebook `Unity Catalog volume for streaming checkpoints.ipynb`.

### Problem: NAT Gateway cost spike

**Symptoms**

- Unexpected Azure bill increase.

**Cause:** Databricks clusters using a NAT Gateway for outbound traffic.

**Fix**

- Remove the unused NAT Gateway.
- Enable cluster auto-termination.

## 3. Fabric Issues

### Problem: Eventstream not receiving events

**Symptoms**

- No rows in Eventhouse.
- Eventstream shows zero throughput.

**Cause:** Event Hub connection misconfigured.

**Fix**

- Reconnect the Event Hub source.
- Validate the consumer group.
- Ensure the Function App is publishing events.

### Problem: Power BI not refreshing

**Symptoms**

- Dashboard shows stale data.

**Cause:** Eventhouse table not updating.

**Fix**

- Run a KQL query to confirm ingestion.
- Check Eventstream routing rules.

## 4. WeatherAPI Issues

### Problem: API returns error string

**Symptoms**

- Downstream code breaks.
- Alerts not parsed.

**Cause:** `handle_response` returns error strings instead of raising exceptions.

**Fix:** Refactor to either:

- Raise exceptions, or
- Return `None` and validate before processing.

## 5. General Best Practices

- Use Managed Identity instead of connection strings.
- Store all secrets in Key Vault.
- Avoid hard-coded city names.
- Add unit tests for data quality.
- Use IaC (Bicep/Terraform) for reproducible deployments.
