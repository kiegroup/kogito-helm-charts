# Kogito Basic Helm Chart
This is a Helm chart to deploy a Kogito application with no frills. 

# Example Usage
By default, this will deploy the basic Kogito [process-quarkus-example](https://github.com/kiegroup/kogito-examples/tree/stable/process-quarkus-example) 
image which I have uploaded onto 
[Quay](https://quay.io/repository/kmok/process-quarkus-example?tab=tags). Follow the [initial setup](../README.md#Usage), then run these commands 
in the base directory of the repository:
## OpenShift
```sh
helm install process-quarkus-example kogito/kogito-basic
export KOGITO_URL=$(kubectl get routes -o jsonpath="{.items[?(@.metadata.name=='process-quarkus-example')].spec.host}")
```

## Other (e.g. minikube)
```sh
helm install --set openshift=false process-quarkus-example kogito/kogito-basic
export KOGITO_URL=$(kubectl get nodes -o jsonpath='{ $.items[0].status.addresses[?(@.type=="InternalIP")].address }'):32000
```

## Example Command
```
> curl -d '{"approver" : "john", "order" : {"orderNumber" : "12345", "shipped" : false}}' -H "Content-Type: application/json" -X POST http://$KOGITO_URL/orders
{"id":"1c7790db-6900-44f0-884f-97a546405fbd","approver":"john","order":{"orderNumber":"12345","shipped":false,"total":0.5123325950331652}}
```
You can find other commands to run in [the example's README](https://github.com/kiegroup/kogito-examples/tree/stable/process-quarkus-example#example-usage).
