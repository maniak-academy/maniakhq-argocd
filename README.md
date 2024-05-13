# kind-cluster-01

kubectl config use-context kubernetes-admin@kubernetes
helm repo add hashicorp https://helm.releases.hashicorp.com

## certify k8sAuthMethodHost
kubectl config view --raw --minify --flatten \ -o jsonpath='{.clusters[].clusters.server}'

## create consul-gossip

kubectl create secret generic consul-gossip-encryption-key --from-literal=key=$(consul keygen) -n consul
export CONSUL_GOSSIP=$(kubectl get --namespace consul secrets/consul-gossip-encryption-key --template={{.data.token}} | base64 -d)

## Get CA and bootsrap 
kubectl get secret consul-ca-cert consul-bootstrap-acl-token --output yaml > cluster1-credentials.yaml -n consul

## Get boostrap token
export CONSUL_HTTP_TOKEN=$(kubectl get --namespace consul secrets/consul-bootstrap-acl-token --template={{.data.token}} | base64 -d)

## license 
secret=$(cat consul.hclic)
kubectl create secret generic consul-ent-license --from-literal="key=${secret}" -n consul

## install 
helm install --values consul/values.yaml consul hashicorp/consul --namespace consul --version "1.4.1"
helm upgrade --values consul/values.yaml consul hashicorp/consul --namespace consul --version "1.4.1"

kubectl create secret generic consul-bootstrap-acl-token --from-literal="token=15b5c9f8-27cb-34cb-189d-e2f1f1dc858f" -n consul 


## install 
helm install --values dataplane.yaml consul hashicorp/consul --namespace consul --version "1.4.1"
helm upgrade --values dataplane.yaml consul hashicorp/consul --namespace consul --version "1.4.1"
helm uninstall consul --namespace consul 

# argoc d 

kubectl get secret argocd-initial-admin-secret -n argocd -o jsonpath="{.data.password}" | base64 --decode
