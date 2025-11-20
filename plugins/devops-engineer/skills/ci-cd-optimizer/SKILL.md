---
name: ci-cd-optimizer
description: Optimize CI/CD pipelines for speed, reliability, and efficiency. Use when improving build times, fixing pipeline failures, or enhancing deployment processes.
---

# CI/CD Optimizer

Optimize CI/CD pipelines for faster builds and reliable deployments.

## Quick Start

Parallelize jobs, cache dependencies, use smaller images, implement health checks, automate rollbacks.

## Instructions

### Pipeline Optimization

**Parallel execution:**
```yaml
# GitHub Actions
jobs:
  test:
    strategy:
      matrix:
        node: [14, 16, 18]
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-node@v3
        with:
          node-version: ${{ matrix.node }}
      - run: npm test
```

**Caching dependencies:**
```yaml
- uses: actions/cache@v3
  with:
    path: ~/.npm
    key: ${{ runner.os }}-node-${{ hashFiles('**/package-lock.json') }}
```

**Docker layer caching:**
```dockerfile
# Order layers by change frequency
FROM node:18-alpine
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build
```

### Deployment Strategies

**Blue-green deployment:**
```yaml
deploy:
  steps:
    - name: Deploy to green
      run: kubectl set image deployment/app app=myapp:${{ github.sha }} --record
    - name: Wait for rollout
      run: kubectl rollout status deployment/app
    - name: Run smoke tests
      run: ./smoke-tests.sh
    - name: Switch traffic
      run: kubectl patch service app -p '{"spec":{"selector":{"version":"green"}}}'
```

**Canary deployment:**
```yaml
- name: Deploy canary (10%)
  run: kubectl set image deployment/app-canary app=myapp:${{ github.sha }}
- name: Monitor metrics
  run: ./check-metrics.sh
- name: Promote to 100%
  run: kubectl set image deployment/app app=myapp:${{ github.sha }}
```

### Best Practices

- Fail fast with linting/tests first
- Use matrix builds for multiple versions
- Cache dependencies and build artifacts
- Use smaller Docker images
- Implement automated rollbacks
- Add health checks
- Monitor pipeline metrics
