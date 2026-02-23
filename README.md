<img width="320" height="320" alt="R (4)" src="https://github.com/user-attachments/assets/f4f64dde-0c1a-4b85-afd9-2edddebc6be6" />

## This is shows instrumenting Python Apps with Prometheus Metrics  

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
