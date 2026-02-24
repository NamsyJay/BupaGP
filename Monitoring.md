## Monitoring Servers with Prometheus & Grafana

Head over to the Graph tab in your Prometheus UI `(http://localhost:9090)` and 
paste these to see your system's health:

Importing from Node Exporter API: Select "Import" on the Dashboard page Select Dashboard ID 1860 (This is the official Node Exporter Full dashboard), 
and it will automatically populate with all your data.

## Basic Queries
### 1. Common Metrics to Query
CPU Usage (Percentage):

`100 - (avg by (instance) (rate(node_cpu_seconds_total{mode="idle"}[5m])) * 100)`

### 2. Memory Usage (Percent- %):

`node_memory_Active_bytes / node_memory_MemTotal_bytes * 100`

### 3. Disk Space Available (GB):

`node_filesystem_avail_bytes{mountpoint="/"} / 1024 / 1024 / 1024`

### Production Environment due to strict HIPAA/HITECH compliance and patient safety requirements.
#### Enhanced Infrastructure Metrics
- IO Wait/Disk Latency: If your Electronic Health Record (EHR) database is slow, doctors can't access patient files.
  Metric: `node_disk_io_time_seconds_total`
- Storage Growth Rates: Healthcare data (scans, logs, records) grows exponentially.
  Use a "Time-to-Full" alert rather than just a 90% threshold.
    Query:
      `predict_linear(node_filesystem_free_bytes[1h], 3600 * 24) < 0`
      (Alerts if the disk will be full in 24 hours).
- Network Errors: Dropped packets in a hospital can break real-time monitoring equipment connections.
  Metric: `node_network_receive_errs_total`
