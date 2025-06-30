# This Fork is from RayanSlim

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
