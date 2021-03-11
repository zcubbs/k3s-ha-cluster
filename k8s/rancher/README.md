## 
Check install status
> kubectl -n cattle-system rollout status deploy/rancher

After Rancher installs successfully:
> kubectl delete ing rancher -n cattle-system

then:
> kubectl apply -f ./ingress.yaml

Websocket issues happen with staging let's encrypt certs

To delete Rancher:
https://rancher.com/docs/rancher/v2.x/en/system-tools/
./system-tools.exe remove -c $HOME\.kube\config  --namespace cattle-system
