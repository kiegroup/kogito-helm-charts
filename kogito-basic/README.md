# Kogito Basic Helm Chart
This is a Helm chart to deploy a Kogito application with no frills. 

# Example Usage
By default, this will deploy the basic Kogito [process-quarkus-example](https://github.com/kiegroup/kogito-examples/tree/stable/process-quarkus-example) 
image which I have uploaded onto 
[Quay](https://quay.io/repository/kmok/process-quarkus-example?tab=tags). Follow the [initial setup](../README.md#Usage), then run these commands:
```sh
helm install process-quarkus-example kogito-basic
curl -d '{"approver" : "john", "order" : {"orderNumber" : "12345", "shipped" : false}}' -H "Content-Type: application/json" -X POST http://$NODE_INTERNAL_IP:32000/orders
```
