consul

helm install consul hashicorp/consul --set global.name=consul --create-namespace --namespace consul --values


helm install --values values.yaml consul hashicorp/consul --namespace consul --version "1.4.1"
helm upgrade --values values.yaml consul hashicorp/consul --namespace consul --version "1.4.1"

export CONSUL_HTTP_TOKEN=$(kubectl get --namespace consul secrets/consul-bootstrap-acl-token --template={{.data.token}} | base64 -d)

helm uninstall prometheus prometheus-community/prometheus -n observability --values prometheus.yaml
 helm install prometheus prometheus-community/prometheus -n observability --values prometheus.yaml
  export POD_NAME=$(kubectl get pods --namespace observability -l "app.kubernetes.io/name=prometheus,app.kubernetes.io/instance=prometheus" -o jsonpath="{.items[0].metadata.name}")

 export POD_NAME=$(kubectl get pods --namespace observability -l "app=prometheus-pushgateway,component=pushgateway" -o jsonpath="{.items[0].metadata.name}")
  kubectl --namespace observability port-forward $POD_NAME 9091

 helm uninstall grafana grafana/grafana --namespace observability --values grafana.yaml

   kubectl get secret --namespace observability grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo

      export NODE_PORT=$(kubectl get --namespace observability -o jsonpath="{.spec.ports[0].nodePort}" services grafana)
     export NODE_IP=$(kubectl get nodes --namespace observability -o jsonpath="{.items[0].status.addresses[0].address}")
     echo http://$NODE_IP:$NODE_PORT