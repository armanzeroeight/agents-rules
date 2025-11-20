---
name: infrastructure-monitor
description: Set up monitoring, logging, and alerting for infrastructure and applications. Use when implementing observability, creating dashboards, or configuring alerts.
---

# Infrastructure Monitor

Set up comprehensive monitoring and observability.

## Quick Start

Use Prometheus for metrics, Grafana for dashboards, Loki for logs, set up alerts for critical issues.

## Instructions

### Metrics with Prometheus

**Application instrumentation:**
```javascript
const prometheus = require('prom-client');

const httpRequestDuration = new prometheus.Histogram({
  name: 'http_request_duration_seconds',
  help: 'Duration of HTTP requests in seconds',
  labelNames: ['method', 'route', 'status_code']
});

app.use((req, res, next) => {
  const start = Date.now();
  res.on('finish', () => {
    const duration = (Date.now() - start) / 1000;
    httpRequestDuration.labels(req.method, req.route?.path, res.statusCode).observe(duration);
  });
  next();
});
```

**Prometheus config:**
```yaml
scrape_configs:
  - job_name: 'app'
    static_configs:
      - targets: ['app:3000']
    scrape_interval: 15s
```

### Dashboards with Grafana

**Key metrics to monitor:**
- Request rate (requests/second)
- Error rate (errors/total requests)
- Response time (p50, p95, p99)
- CPU and memory usage
- Database query time

### Logging with Loki

**Structured logging:**
```javascript
const winston = require('winston');

const logger = winston.createLogger({
  format: winston.format.json(),
  transports: [
    new winston.transports.Console()
  ]
});

logger.info('User logged in', { userId: user.id, ip: req.ip });
```

### Alerting

**Alert rules:**
```yaml
groups:
  - name: app_alerts
    rules:
      - alert: HighErrorRate
        expr: rate(http_requests_total{status=~"5.."}[5m]) > 0.05
        for: 5m
        annotations:
          summary: "High error rate detected"
```

### Best Practices

- Monitor golden signals (latency, traffic, errors, saturation)
- Set up actionable alerts
- Use log aggregation
- Implement distributed tracing
- Create runbooks for alerts
- Regular dashboard reviews
