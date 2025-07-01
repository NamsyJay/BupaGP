# This Fork is from RayanSLim

## This is shows instrumenting Python Apps with Prometheus Metrics  

## STEPS

### 1) Clone The Repository

### 2) Run the docker-compose file

        `docker-compose up -d`

### 3) View the Prometheus Endpoint in the Browser

### 4) Explore The Application Metrics

        - View The Metrics by executing queries. 

### 5) Setup Grafana Dashboard

        - Add the Prometheus Data Source in the GUI.
        - `http:prometheus:9090` (because the prometheus is configured using docker-compose)

### 6) Create The Grafana Dashboard

        - Import Grafana-Dashboard json file.
        - This displays the active queries inside our database.

### 7) Creating Panels In Grafana

### Process Uptime

        - Select Stat as the type in the Add Panel Section.
        - Include Query in the Configuration.

        - `time() - process_start_time_seconds{job="fastapi-app"}`

        # For consistency regardless of the application, we use variables.
        - Head To settings and change the General name to `job`.
        - In the Query section, Grafana has a built-in function called label values                          `lables_values(job)`
        - It scans evry metric named `job`.
        - There is a preview section at the base of the variable > edit.

### Total Requests

        - Make a few requests to 
        - Include Query in the Configuration.
                `sum(increase(http_request_total{job="$job"}[5m]))`
        

### Error Rates

        -
        -

### Average Request Duration
