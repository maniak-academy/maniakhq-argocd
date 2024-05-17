helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
helm repo update

 kubectl create ns ingress-nginx
 helm install ingress-nginx ingress-nginx/ingress-nginx \
    --namespace ingress-nginx --create-namespace \
    --set controller.service.type=NodePort \
    --set controller.service.nodePorts.http=31280 \
    --set controller.service.nodePorts.https=31243 \
    --set controller.hostNetwork=true \
    --set controller.extraArgs.enable-ssl-passthrough=true

 helm uninstall ingress-nginx \
    --namespace ingress-nginx 