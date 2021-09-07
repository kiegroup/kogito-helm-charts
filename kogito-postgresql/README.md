# Kogito PostgreSQL Helm Chart
This is a Helm chart to deploy a Kogito application with PostgreSQL 
persistence. 

# Example Usage
By default, this will deploy the Kogito [process-postgresql-persistence-quarkus](https://github.com/kiegroup/kogito-examples/tree/stable/process-postgresql-persistence-quarkus) 
image which I have uploaded onto 
[Quay](https://quay.io/repository/kmok/process-postgresql-persistence-quarkus?tab=tags). The 
image is compiled using the `jdbc-persistence`. Follow the [initial setup](../README.md#Usage), then run these commands:
```sh
helm install process-postgresql-persistence-quarkus kogito/kogito-postgresql
curl -X POST -H 'Content-Type:application/json' -H 'Accept:application/json' -d '{"name" : "my fancy deal", "traveller" : { "firstName" : "John", "lastName" : "Doe", "email" : "jon.doe@example.com", "nationality" : "American","address" : { "street" : "main street", "city" : "Boston", "zipCode" : "10005", "country" : "US" }}}' http://$NODE_INTERNAL_IP:32000/deals
```

# PostgreSQL Setup
This chart uses Bitnami's [PostgreSQL Helm chart](https://github.com/bitnami/charts/tree/master/bitnami/postgresql) 
to setup the PostgreSQL instance.
Customization of the PostgreSQL instance can be done 
with their parameters listed in [Bitnami's README](https://github.com/bitnami/charts/tree/master/bitnami/postgresql#parameters) 
via the same process of adding the parameters to a custom 
YAML file and passing it into `helm install --values`.

# `init.sql`
This chart initializes a database for the Kogito [process-postgresql-persistence-quarkus 
example](https://github.com/kiegroup/kogito-examples/tree/stable/process-postgresql-persistence-quarkus) by 
default
by passing in an SQL script in `values.yaml`:
```yaml
postgresql:
  initdbScripts:
    init.sql: |
      CREATE ROLE "kogito-user" WITH LOGIN SUPERUSER INHERIT CREATEDB CREATEROLE NOREPLICATION ENCRYPTED PASSWORD 'md54adb613a8ffdd707e032c918d791e2e5';
      CREATE DATABASE kogito WITH OWNER = "kogito-user" ENCODING = 'UTF8' LC_COLLATE = 'en_US.UTF-8' LC_CTYPE = 'en_US.UTF-8' TABLESPACE = pg_default CONNECTION LIMIT = -1;
```
This can be overridden by passing in your own YAML file with 
the same syntax as above but with your own SQL script. Then, 
you just add the `--values` flag with your YAML file name while running the `helm install` command. 
For example:
```
helm install --values sql-script.yaml process-postgresql-persistence-quarkus kogito-postgresql
```

## Concurrency Issues
Since the Kogito application and PostgreSQL instance are 
started at the same time, concurrency issues can occur when 
the Kogito application expects the database to be ready when 
it is not.

### Health Check
Integrating something like the [SmallRye 
Health extension](https://quarkus.io/guides/smallrye-health) 
for a Quarkus Kogito application is strongly recommended. In 
the SmallRye Health documentation, they show how you can 
implement a [readiness health check 
procedure](https://quarkus.io/guides/smallrye-health#adding-a-readiness-health-check-procedure) 
for a database connection. This way, the pod will not accept 
traffic until a connection to the database is established.

### Initializing Necessary Tables
Another concurrency issue that can arise is when the Kogito 
application only creates its necessary tables on startup 
(see [KOGITO-5775](https://issues.redhat.com/browse/KOGITO-5775)). 
In this scenario, even after a connection to the database is established, 
the Kogito application will try to insert a row into a 
non-existent table. There are 2 possible solutions to this 
problem:
1. In the Kogito application's code, check if the table 
exists before inserting into it. If it does not exist, 
create it first.
2. Initialize the table in `init.sql` as a value as 
described in [the section above](#init.sql). This is the 
method used for the default example in this chart.
