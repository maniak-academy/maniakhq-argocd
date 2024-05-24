## How to run 

## Initiate terraform mongodb in aws

```bash
terraform init
terraform apply -auto-approve
```

## apply hashicups

```bash
kubectl create namespace tgw
kubect  apply -f hashicups
```

## explore app 
kubectl get svc -n tgw
NAME         TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)    AGE
frontend     ClusterIP   10.97.70.237     <none>        3000/TCP   116s
nginx        ClusterIP   10.104.208.32    <none>        80/TCP     115s
payments     ClusterIP   10.110.178.221   <none>        8080/TCP   115s
public-api   ClusterIP   10.98.251.148    <none>        8080/TCP   115s

## apply api-gw
```bash
kubect  apply -f api-gw
```

## Register the AWS RDS instance as a Consul service

```bash
export AWS_RDS_ENDPOINT=$(terraform output -raw aws_rds_endpoint) && \
echo $AWS_RDS_ENDPOINT
```

export AWS_RDS_ENDPOINT=learn-consul-yeid.cneg19enouwf.us-west-2.rds.amazonaws.com

## Create a custom Consul service configuration file for managed-aws-rds with envsubst. This will fill all placeholders with your unique AWS RDS private DNS address.

```bash
envsubst < config/external-service.template > config/external-service.json
```


## Register managed-aws-rds as a service in Consul.

```bash
export CONSUL_HTTP_ADDR=https://172.16.10.20:31443
export CONSUL_HTTP_TOKEN=$(kubectl get --namespace consul secrets/consul-bootstrap-acl-token --template={{.data.token}} | base64 -d)
echo $CONSUL_HTTP_TOKEN

curl -k \
   --request PUT \
   --data @config/external-service.json \
   --header "X-Consul-Token: $CONSUL_HTTP_TOKEN" \
   $CONSUL_HTTP_ADDR/v1/catalog/register
```

##  applty service defaults 
Apply the Consul service defaults for the external managed-aws-rdsservice. The configuration in service-defaults.yaml creates a virtual service in Consul, which allows the services within your service mesh to communicate with the external service using Consul DNS.

```bash
kubectl apply --filename config/service-defaults.yaml
```


kubectl exec -it svc/consul-server --namespace consul -- /bin/sh -c "nslookup -port=8600 managed-aws-rds.virtual.consul 127.0.0.1"

## craete policy
consul acl policy create -name "managed-aws-rds-write-policy" \
                       -datacenter "maniak" \
                       -rules @config/write-acl-policy.hcl


## get role id 
export TGW_ACL_ROLE_ID=$(consul acl role list -format=json | jq --raw-output '[.[] | select(.Name | endswith("-terminating-gateway-acl-role"))] | if (. | length) == 1 then (. | first | .ID) else "Unable to determine the role ID because there are multiple roles matching this name.\n" | halt_error end')

TGW_ACL_ROLE_ID=70b2405d-52da-1a5c-e14e-6c2e5942c620

## Attach your custom ACL policy to the terminating gateway role.

$ consul acl role update -id $TGW_ACL_ROLE_ID \
                       -datacenter "maniak" \
                       -policy-name managed-aws-rds-write-policy

kubectl apply --filename config/service-intentions.yaml

kubectl apply --filename config/terminating-gateway.yaml