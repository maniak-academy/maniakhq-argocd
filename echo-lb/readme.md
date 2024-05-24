The following diagram shows your existing Kubernetes cluster, the Consul API Gateway, and the echo sample services. In this section, you will review the HTTPRoute definitions for the echo service and deploy it to split traffic evenly between echo-1 and echo-2.

```bash
APIGW_URL="http://echo.maniak.lab/echo"
for i in `seq 1 10`; do
  echo -n "$i. "
  curl -s $APIGW_URL | jq -r '"service: \(.service), pod: \(.pod)"'
done

```