kubectl get secret consul-consul-ca-cert -o yaml | \
kubectl --context cluster-b apply -f -


kubectl get secret consul-consul-ca-key -o yaml | \
kubectl --context cluster-b apply -f -

kubectl get secret consul-consul-ca-cert -o yaml | \
  kubectl --context cluster-c apply -f -

kubectl get secret consul-consul-ca-key -o yaml | \
  kubectl --context cluster-c apply -f -