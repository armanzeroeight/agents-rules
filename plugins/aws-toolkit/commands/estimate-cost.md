---
description: Estimate AWS costs for planned infrastructure or analyze current spending
allowed-tools: Read, Write
argument-hint: [resource-specifications]
---

# Estimate AWS Cost

Estimate costs for AWS infrastructure based on resource specifications.

## Your Task

Provide cost estimates for AWS resources based on specifications.

### Arguments

- `$1`: Resource specifications (inline or file path)

### Steps

1. **Parse resource specifications:**

   Expected format:
   ```yaml
   region: us-east-1
   resources:
     - type: ec2
       instance_type: t3.medium
       count: 2
       hours_per_month: 730
     - type: rds
       instance_type: db.t3.small
       engine: postgres
       storage_gb: 100
     - type: s3
       storage_gb: 1000
       requests_per_month: 1000000
   ```

2. **Calculate costs by service:**

   **EC2 pricing (us-east-1, approximate):**
   - t3.micro: $0.0104/hour (~$7.50/month)
   - t3.small: $0.0208/hour (~$15/month)
   - t3.medium: $0.0416/hour (~$30/month)
   - t3.large: $0.0832/hour (~$60/month)
   - m5.large: $0.096/hour (~$70/month)
   - m5.xlarge: $0.192/hour (~$140/month)

   **Reserved Instance savings:**
   - 1-year: ~30-40% discount
   - 3-year: ~50-60% discount

   **RDS pricing (us-east-1, approximate):**
   - db.t3.micro: $0.017/hour (~$12/month)
   - db.t3.small: $0.034/hour (~$25/month)
   - db.t3.medium: $0.068/hour (~$50/month)
   - Storage: $0.115/GB-month (gp2)
   - Backup storage: $0.095/GB-month

   **S3 pricing:**
   - Standard storage: $0.023/GB-month (first 50 TB)
   - Standard-IA: $0.0125/GB-month
   - Glacier: $0.004/GB-month
   - PUT requests: $0.005 per 1,000
   - GET requests: $0.0004 per 1,000

   **Data transfer:**
   - Out to internet: $0.09/GB (first 10 TB)
   - Between regions: $0.02/GB
   - CloudFront: $0.085/GB (first 10 TB)

   **Lambda:**
   - $0.20 per 1M requests
   - $0.0000166667 per GB-second

   **DynamoDB:**
   - On-demand: $1.25 per million write requests, $0.25 per million read requests
   - Provisioned: $0.00065 per WCU-hour, $0.00013 per RCU-hour
   - Storage: $0.25/GB-month

3. **Calculate total monthly cost:**

   ```
   EC2: 2 × t3.medium × $30 = $60
   RDS: 1 × db.t3.small × $25 = $25
   RDS Storage: 100 GB × $0.115 = $11.50
   S3 Storage: 1000 GB × $0.023 = $23
   S3 Requests: 1M × $0.005 = $5
   
   Total: $124.50/month
   ```

4. **Add cost optimization recommendations:**

   - Use Reserved Instances for predictable workloads (30-60% savings)
   - Use Spot Instances for fault-tolerant workloads (70-90% savings)
   - Implement S3 lifecycle policies
   - Use auto-scaling to match demand
   - Enable CloudWatch for monitoring

5. **Provide cost breakdown:**

   ```
   Monthly Cost Estimate
   =====================
   
   Compute (EC2):           $60.00
   Database (RDS):          $36.50
   Storage (S3):            $28.00
   Data Transfer:           $10.00
   -------------------------
   Total:                   $134.50/month
   
   Annual Cost:             $1,614.00
   
   With Reserved Instances (1-year):
   Compute (EC2):           $42.00 (30% savings)
   Database (RDS):          $25.55 (30% savings)
   -------------------------
   Total:                   $116.05/month
   Annual Savings:          $221.40
   ```

6. **Include cost factors:**

   - Region pricing varies
   - Data transfer costs
   - Backup and snapshot storage
   - CloudWatch logs and metrics
   - Support plan costs

7. **Provide cost optimization tips:**

   - Right-size instances based on actual usage
   - Use auto-scaling
   - Implement lifecycle policies
   - Delete unused resources
   - Monitor with Cost Explorer

### Examples

**Example 1: Simple web app**
```
/estimate-cost "2 t3.medium EC2, 1 db.t3.small RDS, 100GB S3"
```

**Example 2: From file**
```
/estimate-cost infrastructure-plan.yaml
```

**Example 3: Detailed specification**
```
/estimate-cost "
EC2: 3x m5.large (24/7)
RDS: 1x db.m5.large PostgreSQL, 500GB storage
S3: 5TB storage, 10M requests/month
CloudFront: 2TB data transfer
"
```

## Notes

- Prices are approximate and vary by region
- Use AWS Pricing Calculator for exact quotes: https://calculator.aws
- Consider Reserved Instances for 30-60% savings
- Factor in data transfer costs
- Include backup and snapshot storage
- Add 10-20% buffer for unexpected costs
- Monitor actual costs with Cost Explorer
- Set up billing alerts
- Use tags for cost allocation
