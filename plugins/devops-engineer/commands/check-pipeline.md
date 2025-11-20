---
description: Analyze CI/CD pipeline configuration for optimization opportunities
allowed-tools: Read, Grep
argument-hint: [pipeline-file]
---

# Check Pipeline

Analyze CI/CD pipeline configuration and suggest improvements.

## Your Task

Review pipeline configuration and provide optimization recommendations.

### Arguments

- `$1`: Pipeline configuration file (e.g., .github/workflows/ci.yml, .gitlab-ci.yml)

### Steps

1. **Read pipeline configuration**
2. **Check for common issues:**
   - No caching
   - Sequential jobs that could be parallel
   - Large Docker images
   - Missing health checks
   - No rollback strategy
3. **Provide recommendations:**
   - Add dependency caching
   - Parallelize independent jobs
   - Use multi-stage Docker builds
   - Implement health checks
   - Add automated rollbacks
4. **Estimate improvements:**
   - Build time reduction
   - Reliability improvements
   - Cost savings

### Examples

```
/check-pipeline .github/workflows/ci.yml
```

## Notes

- Focus on build speed and reliability
- Suggest specific code changes
- Prioritize high-impact optimizations
