consul acl policy create -name "example-https-write-policy" -rules @write-policy.hcl

consul acl role list -format=json | jq --raw-output '[.[] | select(.Name | endswith("-terminating-gateway-acl-role"))] | if (. | length) == 1 then (. | first | .ID) else "Unable to determine the role ID because there are multiple roles matching this name.\n" | halt_error end'

consul acl role update -id 70b2405d-52da-1a5c-e14e-6c2e5942c620 -policy-name example-https-write-policy

kubectl exec deploy/static-client -- curl -vvvs --header "Host: example-https.com" http://localhost:1234/


export CONSUL_HTTP_ADDR=https://172.16.10.20:31443
export CONSUL_HTTP_TOKEN=$(kubectl get --namespace consul secrets/consul-bootstrap-acl-token --template={{.data.token}} | base64 -d)
echo $CONSUL_HTTP_TOKEN


curl -k \
   --request PUT \
   --data @external.json \
   --header "X-Consul-Token: $CONSUL_HTTP_TOKEN" \
   $CONSUL_HTTP_ADDR/v1/catalog/register

curl --request PUT --data @external.json --insecure $CONSUL_HTTP_ADDR/v1/catalog/register

consul acl policy create -name "example-https-write-policy" -rules @write-policy.hcl

consul acl role list -format=json | jq --raw-output '[.[] | select(.Name | endswith("-terminating-gateway-acl-role"))] | if (. | length) == 1 then (. | first | .ID) else "Unable to determine the role ID because there are multiple roles matching this name.\n" | halt_error end'



consul acl role update -id 70b2405d-52da-1a5c-e14e-6c2e5942c620 -policy-name example-https-write-policy