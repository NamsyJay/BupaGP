## Monitoring Servers with Prometheus & Grafana

Head over to the Graph tab in your Prometheus UI `(http://localhost:9090)` and 
paste these to see your system's health:

Import from Node Exporter API: Select "Import" on the Dashboard page Select `Dashboard ID 1860` (This is the official Node Exporter Full dashboard), and it will automatically populate with all your data.

## Basic Queries
### 1. Common Metrics to Query
CPU Usage (Percentage):

`100 - (avg by (instance) (rate(node_cpu_seconds_total{mode="idle"}[5m])) * 100)`

### 2. Memory Usage (Percent- %):

`node_memory_Active_bytes / node_memory_MemTotal_bytes * 100`

### 3. Disk Space Available (GB):

`node_filesystem_avail_bytes{mountpoint="/"} / 1024 / 1024 / 1024`

### Production Environment due to strict HIPAA/HITECH compliance and patient safety requirements.
#### 1 Enhanced Infrastructure Metrics
- IO Wait/Disk Latency: If your Electronic Health Record (EHR) database is slow, doctors can't access patient files.
  Metric: `node_disk_io_time_seconds_total`
- Storage Growth Rates: Healthcare data (scans, logs, records) grows exponentially.
  Use a "Time-to-Full" alert rather than just a 90% threshold.
    Query:
      `predict_linear(node_filesystem_free_bytes[1h], 3600 * 24) < 0`
      (Alerts if the disk will be full in 24 hours).
- Network Errors: Dropped packets in a hospital can break real-time monitoring equipment connections.
  Metric: `node_network_receive_errs_total`

#### 2. Security & Compliance Metrics (The "HIPAA" Pillar)
HIPAA mandates that healthcare facilities track access and integrity; Prometheus can be used to set up suspicious patterns.
- Unauthorized Access Attempts: Monitor failed login attempts.
- TLS/SSL Certificate Expiry: Monitor encryption certificates to avoid non-compliance and inaccessibility.
    Tool: Use the Blackbox Exporter to monitor `probe_ssl_earliest_cert_expiry`.   
- Audit Log Heartbeats: HIPPA mandates that audit trails must be captured, ensuring that the logging service actually works.

#### 3. Application-Level ""
All applications must be instrumented with these four metrics.
```
Latency - Can a staff member pull up a chart in seconds?
Traffic - Is there an unexpected spike in patient requests? 
Errors - Are there errors preventing prescriptions and procedures
Saturation - Is the database connection pool occupied?
```

### Critical Advice for Healthcare Data
#### Never include PHI (Protected Health Information) in your Prometheus labels.
- Use generic IDs or anonymized labels only; Do not use `{medical_record_number="12345"}`.
- This would put PHI into your monitoring database, which usually isn't encrypted or audited to the same level as your primary database, creating a massive compliance risk.
