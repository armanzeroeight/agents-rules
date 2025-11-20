---
name: security-group-analyzer
description: Audit AWS security groups for overly permissive rules and security vulnerabilities. Use when reviewing AWS security, auditing security groups, or improving network security posture.
---

# Security Group Analyzer

Audit AWS security groups and identify security vulnerabilities.

## Quick Start

List security groups, check for 0.0.0.0/0 access, restrict to minimum needed ports and IPs.

## Instructions

### Security Group Audit Process

1. **List all security groups**
2. **Identify overly permissive rules**
3. **Check for unused security groups**
4. **Recommend restrictions**
5. **Implement changes**

### List Security Groups

```bash
# List all security groups
aws ec2 describe-security-groups \
  --query 'SecurityGroups[].[GroupId,GroupName,Description]' \
  --output table

# Get specific security group
aws ec2 describe-security-groups \
  --group-ids sg-1234567890abcdef0
```

### Common Security Issues

**1. Open to the world (0.0.0.0/0)**

Find security groups with unrestricted access:
```bash
aws ec2 describe-security-groups \
  --filters "Name=ip-permission.cidr,Values=0.0.0.0/0" \
  --query 'SecurityGroups[].[GroupId,GroupName,IpPermissions[?IpRanges[?CidrIp==`0.0.0.0/0`]]]'
```

**High-risk ports open to 0.0.0.0/0:**
- 22 (SSH)
- 3389 (RDP)
- 3306 (MySQL)
- 5432 (PostgreSQL)
- 27017 (MongoDB)
- 6379 (Redis)

**2. Unrestricted outbound rules**

Default security groups allow all outbound traffic. Restrict if possible:
```bash
# Check outbound rules
aws ec2 describe-security-groups \
  --group-ids sg-1234567890abcdef0 \
  --query 'SecurityGroups[].IpPermissionsEgress'
```

**3. Unused security groups**

Find security groups not attached to any resources:
```bash
# List all security groups
aws ec2 describe-security-groups --query 'SecurityGroups[].GroupId' > all-sgs.txt

# List security groups in use
aws ec2 describe-instances --query 'Reservations[].Instances[].SecurityGroups[].GroupId' > used-sgs.txt
aws rds describe-db-instances --query 'DBInstances[].VpcSecurityGroups[].VpcSecurityGroupId' >> used-sgs.txt

# Compare to find unused
```

### Security Best Practices

**Principle of least privilege:**
- Only allow necessary ports
- Restrict source IPs to minimum needed
- Use security group references instead of CIDR blocks

**SSH/RDP access:**
```bash
# Bad: Open to world
0.0.0.0/0 on port 22

# Good: Restrict to office IP
203.0.113.0/24 on port 22

# Better: Use bastion host or AWS Systems Manager Session Manager
```

**Database access:**
```bash
# Bad: Open to world
0.0.0.0/0 on port 3306

# Good: Only from application security group
sg-app-12345678 on port 3306
```

**Web servers:**
```bash
# Acceptable: HTTP/HTTPS from anywhere
0.0.0.0/0 on port 80, 443

# But use CloudFront or ALB for additional protection
```

### Restricting Security Groups

**Remove overly permissive rule:**
```bash
# Revoke SSH from anywhere
aws ec2 revoke-security-group-ingress \
  --group-id sg-1234567890abcdef0 \
  --protocol tcp \
  --port 22 \
  --cidr 0.0.0.0/0
```

**Add restricted rule:**
```bash
# Allow SSH only from office
aws ec2 authorize-security-group-ingress \
  --group-id sg-1234567890abcdef0 \
  --protocol tcp \
  --port 22 \
  --cidr 203.0.113.0/24
```

**Use security group references:**
```bash
# Allow traffic from another security group
aws ec2 authorize-security-group-ingress \
  --group-id sg-database-12345678 \
  --protocol tcp \
  --port 3306 \
  --source-group sg-app-12345678
```

### Security Group Rules

**Inbound rules structure:**
- Protocol: TCP, UDP, ICMP, or All
- Port range: Single port or range
- Source: CIDR block or security group

**Example secure configuration:**

**Web tier (ALB):**
```
Inbound:
- HTTP (80) from 0.0.0.0/0
- HTTPS (443) from 0.0.0.0/0

Outbound:
- All traffic to app-tier-sg
```

**App tier:**
```
Inbound:
- Port 8080 from web-tier-sg

Outbound:
- Port 3306 to db-tier-sg
- HTTPS (443) to 0.0.0.0/0 (for external APIs)
```

**Database tier:**
```
Inbound:
- Port 3306 from app-tier-sg

Outbound:
- None (or minimal)
```

### Audit Checklist

**Critical issues:**
- [ ] SSH (22) open to 0.0.0.0/0
- [ ] RDP (3389) open to 0.0.0.0/0
- [ ] Database ports open to 0.0.0.0/0
- [ ] All ports open to 0.0.0.0/0

**High priority:**
- [ ] Unused security groups
- [ ] Overly broad CIDR ranges
- [ ] Unnecessary outbound rules
- [ ] Missing descriptions

**Best practices:**
- [ ] Use security group references
- [ ] Tag security groups
- [ ] Document purpose
- [ ] Regular audits

### Common Patterns

**Bastion host:**
```
Bastion SG:
Inbound:
- SSH (22) from office IP only

Private instance SG:
Inbound:
- SSH (22) from bastion-sg only
```

**Load balancer pattern:**
```
ALB SG:
Inbound:
- HTTP/HTTPS from 0.0.0.0/0

App SG:
Inbound:
- App port from alb-sg only
```

**Database pattern:**
```
DB SG:
Inbound:
- DB port from app-sg only
- DB port from bastion-sg (for admin)
```

### Monitoring and Alerts

**CloudWatch Events for security group changes:**
```json
{
  "source": ["aws.ec2"],
  "detail-type": ["AWS API Call via CloudTrail"],
  "detail": {
    "eventName": [
      "AuthorizeSecurityGroupIngress",
      "RevokeSecurityGroupIngress"
    ]
  }
}
```

**AWS Config rules:**
- restricted-ssh: Checks for SSH from 0.0.0.0/0
- restricted-common-ports: Checks for common ports
- vpc-sg-open-only-to-authorized-ports

### Remediation Steps

**For SSH/RDP open to world:**
1. Identify who needs access
2. Get their IP addresses
3. Restrict to those IPs
4. Or use AWS Systems Manager Session Manager

**For database ports open:**
1. Identify application security groups
2. Remove 0.0.0.0/0 rule
3. Add security group reference rules
4. Test connectivity

**For unused security groups:**
1. Verify not in use
2. Document before deletion
3. Delete security group
4. Monitor for issues

### Tools

**AWS Security Hub:**
- Automated security checks
- Compliance standards
- Findings aggregation

**AWS Config:**
- Track security group changes
- Compliance rules
- Automated remediation

**Third-party tools:**
- Prowler (open source)
- CloudMapper
- ScoutSuite

### Example Audit Report

```
Security Group Audit Report
===========================

Critical Issues:
- sg-12345678: SSH (22) open to 0.0.0.0/0
- sg-23456789: MySQL (3306) open to 0.0.0.0/0

High Priority:
- sg-34567890: Unused security group
- sg-45678901: RDP from broad CIDR (10.0.0.0/8)

Recommendations:
1. Restrict SSH to office IP: 203.0.113.0/24
2. Restrict MySQL to app security group: sg-app-12345
3. Delete unused security group: sg-34567890
4. Narrow RDP access to specific subnet

Estimated risk reduction: High
```

## Best Practices

**Regular audits:**
- Weekly: Check for new overly permissive rules
- Monthly: Review all security groups
- Quarterly: Clean up unused security groups

**Documentation:**
- Add descriptions to all security groups
- Document purpose of each rule
- Tag security groups by environment/project

**Automation:**
- Use AWS Config for continuous monitoring
- Set up CloudWatch alarms for changes
- Automate remediation where possible

**Defense in depth:**
- Security groups are one layer
- Also use NACLs, WAF, Shield
- Implement application-level security
