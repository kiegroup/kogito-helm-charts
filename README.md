# Kogito Helm Charts
This repository contains a collection of Helm charts to deploy various Kogito applications with different 
infrastructure requirements. 

# Usage
By default, each chart will deploy a specific [Kogito example](https://github.com/kiegroup/kogito-examples) 
where the chart will deploy any required infrastructure. Each chart's usage section will assume you have the 
following setup:
```sh
kubectl create namespace kogito-helm
git clone https://github.com/Kevin-Mok/kogito-helm-charts.git
cd kogito-helm-charts
export NODE_INTERNAL_IP=$(kubectl get nodes -o jsonpath='{ $.items[0].status.addresses[?(@.type=="InternalIP")].address }')
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
  repository: quay.io/kmok/process-springboot-example
EOF
helm install --namespace kogito-helm --values myvals.yaml process-springboot-example kogito-helm-chart
```

Or in the case like this one where you only have a couple 
values to change, you can also specify values directly in 
the command like so:
```sh
helm install --namespace kogito-helm --set image.repository=quay.io/kmok/process-springboot-example,runtime=springboot process-springboot-example kogito-helm-chart
```

# Applications Exposed By Default
For ease of use and demonstration purposes, the Kogito application will be exposed with a `NodePort` by default and uses port 32000.

# Road Map
## Operator-Less
- PostgreSQL (WIP)
- Kafka
- data index (requires 2 above)

## With Operator
- ~~Prometheus~~
- Infinispan (WIP)
- Kafka (WIP)
- data index (requires 2 above)
