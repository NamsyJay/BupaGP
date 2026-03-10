<img width="1280" height="714" alt="eb3ddb2e-2ee0-4af0-9501-71e4d0d8e465-Photoroom" src="https://github.com/user-attachments/assets/c956d16d-97db-4030-8397-102b38934be5" />

Vitalync helps connect every system, protecting every patient by deliveriing always-on digital helath infrastructure that clinical teams can trust


## Security and Privacy
Vitalync is SOC2 and HIPAA compliant. Learn more about our security practices.

## VITALYNC DASHBOARD
Vitalync brings businsess and hospital data into a centralised dashboard. It serves over 10k plus patients. Through standardisation, consolidatng and hydrating data with medical code, Vitalync delivers rich and comprehensive patient data at the point-of-care.

<img width="1850" height="744" alt="Screenshot 2026-03-09 at 18-25-34 Vitalync — Executive Service Health - Dashboards - Grafana" src="https://github.com/user-attachments/assets/af7d9e7c-4543-4847-a665-1b44e83816ac" />

VITALYNC — Mock Prometheus Exporter                      ║
║         Client Demo Mode · Realistic NHS Service Data            ║
║                                                                  ║
║  Serves live-updating metrics on http://localhost:9100/metrics   ║
║  Pure Python 3 — zero dependencies required 

## STEPS

### 1) Clone The Repository

### 2) Run the docker-compose file

        `docker-compose up -d`

### 3) View the Prometheus Endpoint in the Browser

### 4) Explore The Application Metrics

        - View The Metrics by executing queries. 
        - include additional metrics.

### 5) Setup Grafana Dashboard

        - Add the Prometheus Data Source in the GUI.
        - `http:prometheus:9090` (because Prometheus is configured using docker-compose)

### 6) Create The Grafana Dashboard

        - Import `Grafana-Dashboard json` file.
        - This displays the active queries inside the database.

### 7) Creating Panels In Grafana

### Process Uptime

        - Select Stat as the type in the Add Panel Section.
        - Include Query in the Configuration.

        - `time() - process_start_time_seconds{job="fastapi-app"}`

        # For consistency, regardless of the application, we use variables.
        - Head to settings and change the General name to `job`.
        - In the Query section, Grafana has a built-in function called label values `lables_values(job)`
        - It scans every metric named `job`.
        - There is a preview section at the base of the variable > edit.

### Total Requests

        - Make a few requests to 
        - Include Query in the Configuration.
                `sum(increase(http_request_total{job="$job"}[5m]))`
        

### Error Rates

        - Download an API Testing Tool like Postman; I downloaded the Postman extension in VS Code.
        
        - `(sum((increase(http_request_total{job="fastapi-app, status=~"4.."}[15m]) or vector(0)))) + sum((increase(http_request_total{job="fastapi-app", status=~"5.."}[15m]) or vector)) / 
        sum(increase(http_request_total{job="fastapi-app"}[15m]))`

### Average Request Duration

        Get Rate Request Duration / Total Requests
        - sum(rate(http_request_duration_seconds_sum[30m])) / sum(rate(http_request_duration_seconds_count[30m]))

### Requests In Progress

        sum(http_request_in_progress{job="fastapi-app", path!="/metrics"})  

### Database Query Performance
        Tracks slow queries against patient data store.
        # Average query duration
        rate(pg_stat_statements_total_time_seconds[5m])/rate(pg_stat_statements_calls_total[5m])

        # Slow queries (over 1 second)
        pg_stat_statements_mean_time_seconds > 1

## License

Distributed under the AGPLv3 License. See LICENSE for more information.

Copyright © Vitalync 2025-present
