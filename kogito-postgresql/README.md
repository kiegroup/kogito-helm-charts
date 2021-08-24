# Kogito PostgreSQL Helm Chart
This is a Helm chart to deploy a Kogito application with PostgreSQL 
persistence. 

# Example Usage
By default, this will deploy the Kogito [process-postgresql-persistence-quarkus](https://github.com/kiegroup/kogito-examples/tree/stable/process-postgresql-persistence-quarkus) 
image which I have uploaded onto 
[Quay](https://quay.io/repository/kmok/process-postgresql-persistence-quarkus?tab=tags). The 
image is compiled using the `jdbc-persistence`. Follow the [initial setup](../README.md#Usage), then run these commands:
```sh
helm install process-postgresql-persistence-quarkus kogito-postgresql
curl -X POST -H 'Content-Type:application/json' -H 'Accept:application/json' -d '{"name" : "my fancy deal", "traveller" : { "firstName" : "John", "lastName" : "Doe", "email" : "jon.doe@example.com", "nationality" : "American","address" : { "street" : "main street", "city" : "Boston", "zipCode" : "10005", "country" : "US" }}}' http://$NODE_INTERNAL_IP:32000/deals
```
