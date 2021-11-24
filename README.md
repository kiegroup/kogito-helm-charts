# Kogito Helm Charts
This repository contains a collection of Helm charts to deploy various Kogito applications with different 
infrastructure requirements. 

# Usage
By default, each chart will deploy a specific [Kogito example](https://github.com/kiegroup/kogito-examples) 
where the chart will deploy any required infrastructure. Each chart's usage section will assume you have the 
following setup:
```sh
helm repo add kogito https://kiegroup.github.io/kogito-helm-charts
kubectl create namespace kogito-helm
kubectl config set-context --current --namespace=kogito-helm
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
  repository: quay.io/kiegroup/examples-process-springboot-example
EOF
helm install --namespace kogito-helm --values myvals.yaml process-springboot-example kogito-helm-chart
```

Or in the case like this one where you only have a couple 
values to change, you can also specify values directly in 
the command like so:
```sh
helm install --namespace kogito-helm --set image.repository=quay.io/kiegroup/examples-process-springboot-example,runtime=springboot process-springboot-example kogito-helm-chart
```
Note that the default images used in the charts are quarkus examples.
# Applications Exposed By Default
For ease of use and demonstration purposes, the Kogito application will be exposed by default.
On OpenShift, a `Route` is created by default to expose the application.
On Kubernetes, the application is exposed with a `NodePort` by default and uses port 32000.

Note that OpenShift Route hosts are limited to 63 characters, so long chart names may cause route creation issues.

# Road Map
## Operator-Less
- ~~PostgreSQL~~
- ~~Kafka~~
- data index (requires 2 above)

## With Operator
- ~~Prometheus~~
- KogitoRuntime
- Infinispan
- Kafka
- data index (requires 2 above)
