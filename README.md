# Kogito Helm Chart
This is a Helm chart to deploy a Kogito application with Prometheus integration. 

# Example Usage
By default, this will deploy the basic Kogito [travel agency 
example](https://github.com/kiegroup/kogito-examples/tree/stable/kogito-travel-agency/basic) 
image which I have uploaded onto 
[Quay](https://quay.io/repository/kmok/kogito-travel-agency-basic?tab=tags). 
```sh
kubectl create namespace kogito-helm
git clone https://github.com/Kevin-Mok/kogito-helm-chart.git
helm install --namespace kogito-helm travel-agency-basic kogito-helm-chart
export NODE_INTERNAL_IP=$(kubectl get nodes -o jsonpath='{ $.items[0].status.addresses[?(@.type=="InternalIP")].address }')
curl -H "Content-Type: application/json" -H "Accept: application/json" -X POST "http://$NODE_INTERNAL_IP:32000/travels" -d @- << EOF
{
  "traveller" : {
    "firstName" : "John",
    "lastName" : "Doe",
    "email" : "john.doe@example.com",
    "nationality" : "American",
    "address" : {
      "street" : "main street",
      "city" : "Boston",
      "zipCode" : "10005",
      "country" : "US"
    }
  },
  "trip" : {
    "city" : "New York",
    "country" : "US",
    "begin" : "2019-12-10T00:00:00.000+02:00",
    "end" : "2019-12-15T00:00:00.000+02:00"
  }
}
EOF
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
For ease of use and demonstration purposes, both the Kogito application and Prometheus 
instance will be exposed with `NodePort`'s by default; they use ports 32000 
and 32001 respectively.

# Prometheus Integration
Prometheus monitoring is enabled by default. As such, a 
Prometheus instance will be created upon installation given 
that the Prometheus operator is installed. If not installed, 
please see the [Prometheus operator README](https://github.com/prometheus-operator/prometheus-operator#quickstart) 
for instructions on installing the operator. Note that their 
`bundle.yaml` defaults to installing in the `default` 
namespace.

You can access the web console at `http://$NODE_INTERNAL_IP:32001/graph` following the [Example Usage](#example-usage) above.

To disable the Prometheus integration, you can set the `Values.prometheus.enabled` to `false`. 
