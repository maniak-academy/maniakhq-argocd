helm install ingress-nginx ingress-nginx/ingress-nginx -f values.yaml -n ingress-nginx

kubectl create secret tls foo-tls --key example/tls.key --cert example/tls.crt -n foo
helm install ingress-nginx ingress-nginx/ingress-nginx -f values.yaml -n ingress-nginx

kubectl delete secret tls foo-tls  -n foo
cd ,,