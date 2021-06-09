# Kogito Helm Chart
This is a Helm chart to deploy a simple Kogito application 
with no frills (i.e. no supporting services).

# Example Usage
By default, this will deploy the Kogito [process-quarkus-example](https://github.com/kiegroup/kogito-examples/tree/stable/process-quarkus-example) image which I have uploaded onto [Quay](https://quay.io/repository/kmok/process-quarkus-example?tab=tags). 
You can change this image in [values.yaml](https://github.com/Kevin-Mok/kogito-helm-chart/blob/749901520e4840d55511ceabc62b5feefb1868fc/values.yaml#L7-L11) under the 
`image` key.
```sh
kubectl create namespace kogito-helm
git clone https://github.com/Kevin-Mok/kogito-helm-chart.git
helm install --namespace kogito-helm process-quarkus-example kogito-helm-chart
export POD_NAME=$(kubectl get pods --namespace kogito-helm -l "app.kubernetes.io/name=kogito-app,app.kubernetes.io/instance=process-quarkus-example" -o jsonpath="{.items[0].metadata.name}")
kubectl --namespace kogito-helm port-forward $POD_NAME 8080:8080
# in new terminal
curl -d '{"approver" : "john", "order" : {"orderNumber" : "12345", "shipped" : false}}' -H "Content-Type: application/json" -X POST http://localhost:8080/orders
```
