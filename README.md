# How to get consul dataplane working 
https://developer.hashicorp.com/consul/docs/enterprise/admin-partitions#deploying-consul-with-admin-partitions-on-kubernetes

kubectl config use-context kubernetes-admin@kubernetes
helm repo add hashicorp https://helm.releases.hashicorp.com

# Setup Consul Server
* Create namespace and license

```bash
kubectl create namespace consul
secret=$(cat /Users/sebbycorp/Documents/Projects/maniakhq-argocd/consul-infra/consul.hclic)
kubectl create secret generic consul-ent-license --from-literal="key=${secret}" -n consul
```

## Deploy helm chart 
* Install Helm 
* cd consul-infra/consulcontrolplane 

```bash
helm install consul --values values.yaml  hashicorp/consul --namespace consul --version "1.4.1"
```

* Udpate helm
```bash
helm upgrade consul --values values.yaml  hashicorp/consul --namespace consul --version "1.4.1"
```

## Idenfify the SVC and Node Ports
```bash
kubectl get svc -n consul
```

## Extract Certs 

```bash
kubectl get secret consul-ca-key -n consul -o yaml > consul-ca-key.yaml
kubectl get secret consul-ca-cert -n consul -o yaml > consul-ca-cert.yaml
kubectl get secret consul-partitions-acl-token -n consul -o yaml > consul-partitions-acl-token.yaml

```
## Apply certs and partition token
* consul-ca-key.yaml
* consul-ca-cert.yaml
* consul-partitions-acl-token.yaml

## Install dataplane

```bash
helm install --values dataplane.yaml consul hashicorp/consul --namespace consul --version "1.4.1"
```

## Get took token
export CONSUL_GOSSIP=$(kubectl get --namespace consul secrets/consul-bootstrap-acl-token --template={{.data.token}} | base64 -d)
echo $CONSUL_GOSSIP



## certify k8sAuthMethodHost
kubectl config view --raw --minify --flatten \ -o jsonpath='{.clusters[].clusters.server}'

## create consul-gossip

kubectl create secret generic consul-gossip-encryption-key --from-literal=key=$(consul keygen) -n consul
export CONSUL_GOSSIP=$(kubectl get --namespace consul secrets/consul-bootstrap-acl-token --template={{.data.token}} | base64 -d)
echo $CONSUL_GOSSIP
## Get CA and bootsrap 
kubectl get secret consul-ca-cert consul-bootstrap-acl-token --output yaml > consul1-credentials.yaml -n consul

## Get boostrap token
export CONSUL_HTTP_TOKEN=$(kubectl get --namespace consul secrets/consul-bootstrap-acl-token --template={{.data.token}} | base64 -d)
echo $CONSUL_HTTP_TOKEN
## license 
kubectl create namespace consul
secret=$(cat consul.hclic)
kubectl create secret generic consul-ent-license --from-literal="key=${secret}" -n consul

## install 
helm install --values consul/values.yaml consul hashicorp/consul --namespace consul --version "1.4.1"
helm upgrade consul --values /values.yaml consul hashicorp/consul --namespace consul --version "1.4.1"

kubectl create secret generic consul-bootstrap-acl-token --from-literal="token=15b5c9f8-27cb-34cb-189d-e2f1f1dc858f" -n consul 


## install 
helm install --values dataplane.yaml consul hashicorp/consul --namespace consul --version "1.4.1"
helm upgrade --values dataplane.yaml consul hashicorp/consul --namespace consul --version "1.4.1"
helm uninstall consul --namespace consul 

# argoc d 

kubectl get secret argocd-initial-admin-secret -n argocd -o jsonpath="{.data.password}" | base64 --decode


kubectl get secret consul-ca-cert consul-bootstrap-acl-token  -n consul --output yaml > cluster1-credentials.yaml

