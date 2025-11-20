---
name: etl-designer
description: Design ETL/ELT pipelines with proper orchestration, error handling, and monitoring. Use when building data pipelines, designing data workflows, or implementing data transformations.
---

# ETL Designer

Design robust ETL/ELT pipelines for data processing.

## Quick Start

Use Airflow for orchestration, implement idempotent operations, add error handling, monitor pipeline health.

## Instructions

### Airflow DAG Structure

```python
from airflow import DAG
from airflow.operators.python import PythonOperator
from datetime import datetime, timedelta

default_args = {
    'owner': 'data-team',
    'retries': 3,
    'retry_delay': timedelta(minutes=5),
    'email_on_failure': True,
    'email': ['alerts@company.com']
}

with DAG(
    'etl_pipeline',
    default_args=default_args,
    schedule_interval='0 2 * * *',  # Daily at 2 AM
    start_date=datetime(2024, 1, 1),
    catchup=False
) as dag:
    
    extract = PythonOperator(
        task_id='extract_data',
        python_callable=extract_from_source
    )
    
    transform = PythonOperator(
        task_id='transform_data',
        python_callable=transform_data
    )
    
    load = PythonOperator(
        task_id='load_to_warehouse',
        python_callable=load_to_warehouse
    )
    
    extract >> transform >> load
```

### Incremental Processing

```python
def extract_incremental(last_run_date):
    query = f"""
        SELECT * FROM source_table
        WHERE updated_at > '{last_run_date}'
    """
    return pd.read_sql(query, conn)
```

### Error Handling

```python
def safe_transform(data):
    try:
        transformed = transform_data(data)
        return transformed
    except Exception as e:
        logger.error(f"Transform failed: {e}")
        send_alert(f"Pipeline failed: {e}")
        raise
```

### Best Practices

- Make operations idempotent
- Use incremental processing
- Implement proper error handling
- Add monitoring and alerts
- Use data quality checks
- Document pipeline logic
