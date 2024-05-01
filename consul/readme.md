consul

helm install consul hashicorp/consul --set global.name=consul --create-namespace --namespace consul --values


helm install --values values.yaml consul hashicorp/consul --namespace consul --version "1.4.1"
helm upgrade --values values.yaml consul hashicorp/consul --namespace consul --version "1.4.1"

export CONSUL_HTTP_TOKEN=$(kubectl get --namespace consul secrets/consul-bootstrap-acl-token --template={{.data.token}} | base64 -d)