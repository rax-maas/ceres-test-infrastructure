# Ceres Test Infrastructure
This repo contains test infrastructure related files. </br>
`docker-compose.yml` file contains following services:
- `zookeeper` and `kafka` - Here `Kafka` is used for creating source data when using it in `Ingestion-Service` microservice.
- `influxdb` service is used to store the output of `Ingestion-Service` microservice.
- `redis` service is used in tenant-routing-service.

## How to run
`docker-compose up -d` </br>
This will create needed containers `kafka`, `influxdb`, and `redis`
