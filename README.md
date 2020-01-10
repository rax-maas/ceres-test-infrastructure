# Ceres Test Infrastructure
This repo contains test infrastructure related files. </br>
`docker-compose.yml` file contains following services:
- `zookeeper` and `kafka` - Here `Kafka` is used for creating source data when using it in `Ingestion-Service` microservice.
- `influxdb` service is used to store the output of `Ingestion-Service` microservice.
- `redis` service is used in tenant-routing-service.

## How to run
`docker-compose up -d` </br>
This will create needed containers `kafka`, `influxdb`, `redis`, and `repose-public`
Define these local environment variables (i.e. shell export) before running docker-compose:

 `KEYSTONE_USER`, `KEYSTONE_PASSWORD` - An account with high-level access to 
 rackspace authentication. This is so repose can forward necessary auth 
 information to the query service. See `https://github.com/racker/secure` 
 
 `BACKEND_HOST` - Network-resolvable IP or HOST of the machine running this
 infrastructure test setup. This is because currently query service does not run
 from a container yet, but is simply tested locally instead.

To check repose (or other services) logs while running, 
`docker-compose logs -f repose-public`

To take down all the containers
`docker-compose down`

`Insomnia_TestQS.yaml` contains a small test-call that can be run with insomnia
to verify that the Query Service (running locally) is working and that repose
is passing requests to it successfully.
