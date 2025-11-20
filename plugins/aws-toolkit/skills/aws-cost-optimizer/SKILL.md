---
name: aws-cost-optimizer
description: Analyze and optimize AWS costs with recommendations for Reserved Instances, right-sizing, and resource cleanup. Use when reducing AWS spending, analyzing costs, or optimizing cloud infrastructure expenses.
---

# AWS Cost Optimizer

Analyze and optimize AWS costs with actionable recommendations.

## Quick Start

Review Cost Explorer, identify top costs, right-size instances, use Reserved Instances, delete unused resources.

## Instructions

### Cost Analysis Process

1. **Identify cost drivers**
2. **Analyze usage patterns**
3. **Recommend optimizations**
4. **Estimate savings**
5. **Implement changes**

### Using AWS Cost Explorer

**Access Cost Explorer:**
- AWS Console → Billing → Cost Explorer
- View costs by service, region, tag

**Key metrics to check:**
- Monthly costs by service
- Cost trends over time
- Unused or underutilized resources
- Data transfer costs

### EC2 Cost Optimization

**Right-sizing instances:**

Check utilization:
```bash
# Get CloudWatch metrics
aws cloudwatch get-metric-statistics \
  --namespace AWS/EC2 \
  --metric-name CPUUtilization \
  --dimensions Name=InstanceId,Value=i-1234567890abcdef0 \
  --start-time 2024-01-01T00:00:00Z \
  --end-time 2024-01-31T23:59:59Z \
  --period 3600 \
  --statistics Average
```

**Recommendations:**
- CPU < 20%: Downsize instance type
- CPU > 80%: Upsize or add instances
- Memory < 50%: Consider smaller instance

**Reserved Instances:**
- 1-year: ~30-40% savings
- 3-year: ~50-70% savings
- Best for predictable workloads

**Savings Plans:**
- More flexible than Reserved Instances
- Commit to $/hour usage
- Apply across instance families

**Spot Instances:**
- Up to 90% savings
- For fault-tolerant workloads
- Batch processing, CI/CD, testing

**Stop unused instances:**
```bash
# Find stopped instances
aws ec2 describe-instances \
  --filters "Name=instance-state-name,Values=stopped" \
  --query 'Reservations[].Instances[].[InstanceId,Tags[?Key==`Name`].Value|[0]]'

# Terminate if not needed
aws ec2 terminate-instances --instance-ids i-1234567890abcdef0
```

### S3 Cost Optimization

**Lifecycle policies:**
```json
{
  "Rules": [{
    "Id": "Archive old data",
    "Status": "Enabled",
    "Transitions": [
      {
        "Days": 30,
        "StorageClass": "STANDARD_IA"
      },
      {
        "Days": 90,
        "StorageClass": "GLACIER"
      }
    ],
    "Expiration": {
      "Days": 365
    }
  }]
}
```

**Storage classes:**
- Standard: Frequent access
- Standard-IA: Infrequent access (30+ days)
- Glacier: Archive (90+ days)
- Glacier Deep Archive: Long-term archive

**Delete incomplete multipart uploads:**
```bash
aws s3api list-multipart-uploads --bucket my-bucket
# Set lifecycle rule to abort after 7 days
```

**Analyze storage:**
```bash
# Get bucket size
aws s3 ls s3://my-bucket --recursive --summarize
```

### RDS Cost Optimization

**Right-size databases:**
- Check CPU, memory, IOPS utilization
- Downsize if consistently low
- Use Aurora Serverless for variable workloads

**Reserved Instances:**
- 1-year: ~30-40% savings
- 3-year: ~50-60% savings

**Stop dev/test databases:**
```bash
# Stop RDS instance
aws rds stop-db-instance --db-instance-identifier mydb

# Start when needed
aws rds start-db-instance --db-instance-identifier mydb
```

**Delete old snapshots:**
```bash
# List snapshots
aws rds describe-db-snapshots --query 'DBSnapshots[?SnapshotCreateTime<`2023-01-01`]'

# Delete old snapshots
aws rds delete-db-snapshot --db-snapshot-identifier snapshot-id
```

### Data Transfer Costs

**Reduce data transfer:**
- Use CloudFront for content delivery
- Keep data in same region
- Use VPC endpoints for AWS services
- Compress data before transfer

**VPC endpoints:**
```bash
# Create S3 VPC endpoint (no data transfer charges)
aws ec2 create-vpc-endpoint \
  --vpc-id vpc-12345678 \
  --service-name com.amazonaws.us-east-1.s3 \
  --route-table-ids rtb-12345678
```

### EBS Cost Optimization

**Delete unattached volumes:**
```bash
# Find unattached volumes
aws ec2 describe-volumes \
  --filters "Name=status,Values=available" \
  --query 'Volumes[].[VolumeId,Size,VolumeType]'

# Delete if not needed
aws ec2 delete-volume --volume-id vol-1234567890abcdef0
```

**Delete old snapshots:**
```bash
# List old snapshots
aws ec2 describe-snapshots --owner-ids self \
  --query 'Snapshots[?StartTime<`2023-01-01`]'

# Delete
aws ec2 delete-snapshot --snapshot-id snap-1234567890abcdef0
```

**Use gp3 instead of gp2:**
- gp3 is 20% cheaper
- Better performance
- Migrate existing volumes

### Lambda Cost Optimization

**Optimize memory allocation:**
- More memory = faster execution = lower cost
- Test different memory settings
- Use AWS Lambda Power Tuning tool

**Reduce cold starts:**
- Use provisioned concurrency (if needed)
- Keep functions warm with scheduled events
- Minimize dependencies

**Monitor invocations:**
```bash
# Get Lambda metrics
aws cloudwatch get-metric-statistics \
  --namespace AWS/Lambda \
  --metric-name Invocations \
  --dimensions Name=FunctionName,Value=my-function \
  --start-time 2024-01-01T00:00:00Z \
  --end-time 2024-01-31T23:59:59Z \
  --period 86400 \
  --statistics Sum
```

### CloudWatch Costs

**Reduce log retention:**
```bash
# Set log retention to 7 days
aws logs put-retention-policy \
  --log-group-name /aws/lambda/my-function \
  --retention-in-days 7
```

**Delete unused log groups:**
```bash
# List log groups
aws logs describe-log-groups

# Delete
aws logs delete-log-group --log-group-name /aws/lambda/old-function
```

### Unused Resources

**Find unused resources:**

**Elastic IPs not attached:**
```bash
aws ec2 describe-addresses \
  --query 'Addresses[?AssociationId==null]'
```

**Load balancers with no targets:**
```bash
aws elbv2 describe-load-balancers
aws elbv2 describe-target-health --target-group-arn arn
```

**NAT Gateways with low traffic:**
```bash
# Check CloudWatch metrics for BytesOutToDestination
```

## Cost Optimization Checklist

**Compute:**
- [ ] Right-size EC2 instances
- [ ] Use Reserved Instances for predictable workloads
- [ ] Use Spot Instances for fault-tolerant workloads
- [ ] Stop/terminate unused instances
- [ ] Implement auto-scaling

**Storage:**
- [ ] Implement S3 lifecycle policies
- [ ] Delete old EBS snapshots
- [ ] Delete unattached EBS volumes
- [ ] Use appropriate S3 storage classes
- [ ] Delete incomplete multipart uploads

**Database:**
- [ ] Right-size RDS instances
- [ ] Use Reserved Instances
- [ ] Stop dev/test databases when not in use
- [ ] Delete old RDS snapshots
- [ ] Consider Aurora Serverless

**Networking:**
- [ ] Use CloudFront to reduce data transfer
- [ ] Delete unused Elastic IPs
- [ ] Use VPC endpoints
- [ ] Review NAT Gateway usage

**Monitoring:**
- [ ] Reduce CloudWatch log retention
- [ ] Delete unused log groups
- [ ] Review custom metrics

**General:**
- [ ] Enable AWS Cost Anomaly Detection
- [ ] Set up billing alerts
- [ ] Use AWS Budgets
- [ ] Tag resources for cost allocation
- [ ] Review Cost Explorer regularly

## Savings Estimation

**Reserved Instances:**
- 1-year, no upfront: ~30% savings
- 1-year, all upfront: ~40% savings
- 3-year, all upfront: ~60% savings

**Spot Instances:**
- 70-90% savings vs On-Demand

**S3 Lifecycle:**
- Standard-IA: ~50% cheaper than Standard
- Glacier: ~80% cheaper than Standard

**Right-sizing:**
- Typical savings: 20-40% on oversized instances

## Tools and Commands

**AWS Cost Explorer:**
- View costs by service, region, tag
- Forecast future costs
- Identify cost anomalies

**AWS Budgets:**
```bash
# Create budget
aws budgets create-budget \
  --account-id 123456789012 \
  --budget file://budget.json
```

**AWS Trusted Advisor:**
- Cost optimization recommendations
- Underutilized resources
- Idle resources

**Third-party tools:**
- CloudHealth
- CloudCheckr
- Spot.io

## Best Practices

**Tagging strategy:**
- Tag all resources
- Use tags for cost allocation
- Common tags: Environment, Project, Owner, CostCenter

**Regular reviews:**
- Weekly: Check for anomalies
- Monthly: Review Cost Explorer
- Quarterly: Optimize Reserved Instances

**Automation:**
- Auto-stop dev instances at night
- Auto-delete old snapshots
- Auto-scale based on demand

**Monitoring:**
- Set up billing alerts
- Use AWS Budgets
- Enable Cost Anomaly Detection
