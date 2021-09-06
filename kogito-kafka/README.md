# Kogito PostgreSQL Helm Chart
This is a Helm chart to deploy a Kogito application with PostgreSQL 
persistence. 

# Example Usage
By default, this will deploy the Kogito [process-kafka-quickstart-quarkus](https://github.com/kiegroup/kogito-examples/tree/stable/process-kafka-quickstart-quarkus) 
image which I have uploaded onto 
[Quay](https://quay.io/repository/kmok/process-kafka-quickstart-quarkus?tab=tags). Follow the [initial setup](../README.md#Usage), then run these commands:
```sh
helm install process-kafka-quickstart-quarkus kogito/kogito-kafka --render-subchart-notes
kubectl run process-kafka-quickstart-quarkus-kafka-client --restart='Never' --image docker.io/bitnami/kafka:2.8.0-debian-10-r57 --command -- sleep infinity  
kubectl exec --tty -i process-kafka-quickstart-quarkus-kafka-client -- bash
kafka-console-producer.sh \
  --broker-list process-kafka-quickstart-quarkus-kafka-0.process-kafka-quickstart-quarkus-kafka-headless.kogito.svc.cluster.local:9092 \
  --topic travellers
{"specversion": "0.3","id": "21627e26-31eb-43e7-8343-92a696fd96b1","source": "","type": "travellers", "time": "2019-10-01T12:02:23.812262+02:00[Europe/Warsaw]","data": { "firstName" : "Jan", "lastName" : "Kowalski", "email" : "jan.kowalski@example.com", "nationality" : "Polish"}}
# open new terminal
kubectl exec --tty -i process-kafka-quickstart-quarkus-kafka-client -- bash
kafka-console-consumer.sh \
  --bootstrap-server process-kafka-quickstart-quarkus-kafka.kogito.svc.cluster.local:9092 \
  --topic processedtravellers \
  --from-beginning     
```

# Customizing 
This chart uses Bitnami's [Kafka Helm chart](https://github.com/bitnami/charts/tree/master/bitnami/kafka) 
to setup the Kafka cluster. By default, the `extraDeploy` 
parameter is used to create a `ConfigMap` to connect the 
Kogito deployment to the Kafka cluster. More customization of the Kafka instance can be done 
with their parameters listed in [Bitnami's README](https://github.com/bitnami/charts/tree/master/bitnami/kafka#parameters) 
via the process of adding the parameters to a custom 
YAML file and passing it into `helm install --values`.
