# Kogito Helm Chart
This is a Helm chart to deploy a simple Kogito application 
with no frills (i.e. no supporting services).

# Example Usage
By default, this will deploy the Kogito [process-quarkus-example](https://github.com/kiegroup/kogito-examples/tree/stable/process-quarkus-example) image which I have uploaded onto [Quay](https://quay.io/repository/kmok/process-quarkus-example?tab=tags). 
```sh
kubectl create namespace kogito-helm
git clone https://github.com/Kevin-Mok/kogito-helm-chart.git
helm install --namespace kogito-helm process-quarkus-example kogito-helm-chart
export POD_NAME=$(kubectl get pods --namespace kogito-helm -l "app.kubernetes.io/name=kogito-app,app.kubernetes.io/instance=process-quarkus-example" -o jsonpath="{.items[0].metadata.name}")
kubectl --namespace kogito-helm port-forward $POD_NAME 8080:8080
# in new terminal
curl -d '{"approver" : "john", "order" : {"orderNumber" : "12345", "shipped" : false}}' -H "Content-Type: application/json" -X POST http://localhost:8080/orders
```

## Customizing Values
To change the image deployed or any of the other default 
values specified in [values.yaml](values.yaml), you can 
simply create another values file and run `helm install` 
with the `--values` flag and your values file name. For example:
```sh
cat << EOF > myvals.yaml
runtime: springboot
image:
  repository: quay.io/kmok/process-springboot-example
EOF
helm install --namespace kogito-helm process-springboot-example kogito-helm-chart
```

Or in the case like this one where you only have a couple 
values to change, you can also specify values directly in 
the command like so:
```sh
helm install --namespace kogito-helm --set image.repository=quay.io/kmok/process-springboot-example,runtime=springboot process-springboot-example kogito-helm-chart
```
