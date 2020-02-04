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

Testing with Grafana:

Again, ensure BACKEND_HOST points to a local computer IP that can be resolved
from inside a container. Then, obtain a (regular, not admin - you can use a test
account specific to our data or your own personal account) X_AUTH rackspace
token, like so:
```
curl https://identity.api.rackspacecloud.com/v2.0/tokens  \
    -X POST \
    -d '{"auth":{"passwordCredentials":{"username":"{account username}","password":"{account password}"}}}' \
    -H "Content-type: application/json" | python -m json.tool
```

You can test the token by running the Insomnia API call against repose/qs, and
check the repose logs and query service logs.

When making the grafana connection to query service (using influxdb), that token
must be passed to the repose frontend.

The grafana<->repose<->query service setup process will be simplified going 
forward.
