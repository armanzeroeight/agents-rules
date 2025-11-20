---
name: data-quality-checker
description: Implement data quality checks, validation rules, and monitoring. Use when ensuring data quality, validating data pipelines, or implementing data governance.
---

# Data Quality Checker

Implement comprehensive data quality checks and validation.

## Quick Start

Use Great Expectations for validation, implement schema checks, monitor data quality metrics, set up alerts.

## Instructions

### Great Expectations Setup

```python
import great_expectations as gx

context = gx.get_context()

# Create expectation suite
suite = context.add_expectation_suite("data_quality_suite")

# Add expectations
validator = context.get_validator(
    batch_request=batch_request,
    expectation_suite_name="data_quality_suite"
)

# Schema validation
validator.expect_table_columns_to_match_ordered_list(
    column_list=["id", "name", "email", "created_at"]
)

# Null checks
validator.expect_column_values_to_not_be_null("email")

# Value ranges
validator.expect_column_values_to_be_between("age", min_value=0, max_value=120)

# Uniqueness
validator.expect_column_values_to_be_unique("email")

# Run validation
results = validator.validate()
```

### Custom Validation Rules

```python
def validate_data_quality(df):
    issues = []
    
    # Check for nulls
    null_counts = df.isnull().sum()
    if null_counts.any():
        issues.append(f"Null values found: {null_counts[null_counts > 0]}")
    
    # Check for duplicates
    duplicates = df.duplicated().sum()
    if duplicates > 0:
        issues.append(f"Found {duplicates} duplicate rows")
    
    # Check data freshness
    max_date = df['created_at'].max()
    if (datetime.now() - max_date).days > 1:
        issues.append("Data is stale")
    
    return issues
```

### Data Quality Metrics

```python
def calculate_quality_metrics(df):
    return {
        'completeness': 1 - (df.isnull().sum().sum() / df.size),
        'uniqueness': df.drop_duplicates().shape[0] / df.shape[0],
        'validity': (df['email'].str.contains('@').sum() / len(df)),
        'timeliness': (datetime.now() - df['created_at'].max()).days
    }
```

### Best Practices

- Validate at ingestion
- Monitor quality metrics
- Set up alerts for failures
- Document quality rules
- Regular quality audits
- Track quality trends
