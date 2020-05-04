# Ceres Test Infrastructure
This repo contains test infrastructure related files.

The [`docker-compose.yml`](docker-compose.yml) file contains following services:
- `zookeeper` and `kafka` - Here `Kafka` is used for creating source data when using it in `Ingestion-Service` microservice.
- `influxdb` service is used to store the output of `Ingestion-Service` microservice.
- `redis` service is used in tenant-routing-service.
- `grafana` which can be used to test the Grafan datasource endpoint of the query service

## How to run

`docker-compose up -d main`

This will create containers needed by the applications: `kafka`, `influxdb`, and `redis`
 
To check kafka (or other services) logs while running, 
`docker-compose logs -f kafka`

To take down all the containers
`docker-compose down`

`Insomnia_TestQS.yaml` contains a small test-call that can be run with insomnia
to verify that the Query Service (running locally) is working and that repose
is passing requests to it successfully.

## HTTP Access

### Direct

For most testing scenarios you can bypass Repose and "impersonate" any tenant by directly
accessing the query service bound at port 8080 and passing the desired tenant via the
`X-Tenant-Id` header. The `X-Roles` header must also be populated with the value "compute:default".

For example, the following would retrieve measurements for a tenant included in the 
metrics produced by "data-generator":

```
tenant=MAAS-account-name-1
curl \
  -H X-Tenant-Id:$tenant \
  -H X-Roles:compute:default \
  localhost:8080/v1.0/tenant/$tenant/intelligence-format-query/measurements
```

### Via Repose

Define these environment variables (i.e. shell export) before running `docker-compose` to bring up the `repose-public` service:

 `KEYSTONE_USER`, `KEYSTONE_PASSWORD` - An account with high-level access to 
 rackspace authentication. This is so repose can forward necessary auth 
 information to the query service. See `https://github.com/racker/secure` 

To ensure the repose container is running use:
```
docker-compose up -d repose-public
```

Obtain a (regular, not admin - you can use a test
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

## Using Grafana

To ensure the grafana container is running use:
```
docker-compose up -d grafana
```

Grafana can be accessed at http://localhost:3000. The default credentials are admin/admin. When prompted to change the password you can always "change" it to "admin" again to keep access simple given that it is a local setup.

A persistent volume is attached to Grafana for storage of dashboards and other configuration. Grafana comes pre-configured with three data sources:

Datasource | Description | Editable?
-----------|-------------|--------------
Query Service | Accesses Ceres data via the Grafana query endpoint. | Yes, such as changing database
InfluxDB Direct | Also access Ceres data, but directly from InfluxDB | Yes, such as changing database
Ceres App Metrics | Provides access to the Spring Boot application metrics | No
