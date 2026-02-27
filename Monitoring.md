## Monitoring Servers with Prometheus & Grafana

Head over to the Graph tab in your Prometheus UI `(http://localhost:9090)` and 
paste these to see your system's health:

Import from Node Exporter API: Select "Import" on the Dashboard page Select `Dashboard ID 1860` (This is the official Node Exporter Full dashboard), and it will automatically populate with all your data.

### Production Environment due to strict HIPAA/HITECH compliance and patient safety requirements.
#### 1 Enhanced Infrastructure Metrics
- IO Wait/Disk Latency: If your Electronic Health Record (EHR) database is slow, doctors can't access patient files.
  Metric: `node_disk_io_time_seconds_total`
- Storage Growth Rates: Healthcare data (scans, logs, records) grows exponentially.
  Use a "Time-to-Full" alert rather than just a 90% threshold.
    Query:
      ```
  predict_linear(node_filesystem_free_bytes[1h], 3600 * 24) < 0
      ```
      (Alerts if the disk will be full in 24 hours).
- Network Errors: Dropped packets in a hospital can break real-time monitoring equipment connections.
  Metric:
  ```
  node_network_receive_errs_total
  ```
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

### For Production Grade Monitoring Dashboards 
#### In real enterprise environments (healthcare, SaaS), 
- SREs monitor for optimise for:
    Signal over noise
    Fast triage
    Clean alerts
    Minimal visual clutter
    Fewer fragile queries

## Basic Queries
### 1. CPU gauge
CPU Usage (Percentage):

`100 - (avg by (instance) (rate(node_cpu_seconds_total{mode="idle"}[5m])) * 100)`

### 2. Disk Space Used
#### Allows only real Linux filesystem
```
100 * (
  1 -
  (
    node_filesystem_avail_bytes{
      instance="$node",
      fstype!~"tmpfs|overlay|squashfs|nsfs|autofs",
      mountpoint!~"^/(run|proc|sys|dev|snap|mnt/wsl|mnt/wslg).*"
    }
    /
    node_filesystem_size_bytes{
      instance="$node",
      fstype!~"tmpfs|overlay|squashfs|nsfs|autofs",
      mountpoint!~"^/(run|proc|sys|dev|snap|mnt/wsl|mnt/wslg).*"
    }
  )
)
```

### 3.Inode Usage Panel
### This combination prevents:
- silent filesystem death
- Docker small-file explosions
- log rotation failures
- Kubernetes volume crashes

```
100 * (
  1 -
  (
    node_filesystem_files_free{
      instance="$node",
      fstype=~"ext4|xfs|btrfs",
      mountpoint!~"^/(run|proc|sys|dev|snap|mnt|usr/lib).*"
    }
    /
    node_filesystem_files{
      instance="$node",
      fstype=~"ext4|xfs|btrfs",
      mountpoint!~"^/(run|proc|sys|dev|snap|mnt|usr/lib).*"
    }
  )
)
```

### 4. Disk Latency
#### This metric explains slow applications instantly.

```
rate(node_disk_read_time_seconds_total{instance="$node"}[5m])
/
rate(node_disk_reads_completed_total{instance="$node"}[5m])
```

```
rate(node_disk_write_time_seconds_total{instance="$node"}[5m])
/
rate(node_disk_writes_completed_total{instance="$node"}[5m])
```

### 5. System Availability (Last 30 Days)
### This is executive-friendly and powerful.

```
100 * avg_over_time(up{instance="$node"}[30d])
```
Set:
- Visualization → Stat
- Unit → Percent (0-100)
- Calculation → Last

### Why This Is Powerful
You can say:
“The system maintained 99.99% availability over the last 30 days.”

That translates directly into:
- Trust.
- SLA discussion.
- Revenue protection.
- Compliance posture.
