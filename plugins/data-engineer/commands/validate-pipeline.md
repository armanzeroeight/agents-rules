---
description: Validate data pipeline configuration and data quality rules
allowed-tools: Read, Grep
argument-hint: [pipeline-file]
---

# Validate Pipeline

Validate data pipeline configuration and suggest improvements.

## Your Task

Review pipeline configuration and data quality checks.

### Arguments

- `$1`: Pipeline configuration file (e.g., dags/etl_pipeline.py)

### Steps

1. **Read pipeline configuration**
2. **Check for:**
   - Error handling
   - Retry logic
   - Monitoring
   - Data quality checks
   - Idempotency
3. **Provide recommendations:**
   - Add missing error handling
   - Implement data quality checks
   - Add monitoring and alerts
   - Ensure idempotent operations

### Examples

```
/validate-pipeline dags/etl_pipeline.py
```

## Notes

- Focus on reliability and data quality
- Suggest specific improvements
- Check for best practices
