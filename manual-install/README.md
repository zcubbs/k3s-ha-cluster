# Install K3S HA Cluster

> Requirement: A postgres database.

Rancher docs here: https://rancher.com/docs/k3s/latest/en/

> As of writing Rancher (stable) does not support k8s 1.20 so install a version of k3s that provides k8s < 1.20. (if you want to use Rancher)
### On each control node
For installation with external loadbalancer add `--tls-san`. And to have true HA add `--node-taint CriticalAddonsOnly=true:NoExecute` to make sure control nodes can't be scheduled for regular workloads.
```console
bash:~$ export K3S_DATASTORE_ENDPOINT='postgres://<DB_USER>:<DB_PASSWORD>@<DB_HOSTNAME>:<DB_PORT>/k3s' k3s server
```
> append ```?sslmode=disable``` if needed
```console
bash:~$ sudo curl -sfL https://get.k3s.io | sh -s - server --node-taint CriticalAddonsOnly=true:NoExecute --tls-san <LOADBALANCER_IP>
```

To install K3s without traefik append ```--disable traefik``` to above command.
> k3s (as of writing) comes with version 1.7 of Traefik. Which is currently at version 2.

Check node status after install
```console
bash:~$ sudo kubectl get nodes
```

#### Inside one of the control nodes execute:
```console
bash:~$ sudo cat /var/lib/rancher/k3s/server/node-token
```

### Inside each worker node

```console
bash:~$ sudo curl -sfL https://get.k3s.io | K3S_URL=https://<LOADBALANCER_IP>:6443 K3S_TOKEN=<SECRET_TOKEN> sh -
```

### Copy kubeconfig to local machine:
After workers are ready:
```console
bash:~$ sudo cat /etc/rancher/k3s/k3s.yaml
```

### Install kubernetes dashboard
If your want kubernetes dashboard:
```console
bash:~$ kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.0.5/aio/deploy/recommended.yaml

bash:~$ kubectl create -f dashboard.admin-user.yml -f dashboard.admin-user-role.yml
```
Get token on linux:
```console
bash:~$ kubectl -n kubernetes-dashboard describe secret admin-user-token | grep ^token
```
for windows:
```console
cmd:~$ kubectl -n kubernetes-dashboard describe secret admin-user-token
```

### Enable Traefik dashboard (If k3s was deployed with Traefik)

```console
bash:~$ kubectl -n kube-system describe deploy traefik
bash:~$ kubectl -n kube-system edit cm traefik
```

then add: under [traefikLog]
```yaml
   [api]
      dashboard = true
```

Scale deploy to 0 then back to 1:

```console
bash:~$ kubectl -n kube-system scale deploy traefik --replicas 0
bash:~$ kubectl -n kube-system scale deploy traefik --replicas 1
bash:~$ kubectl -n kube-system get all
```
To access dashboard on localhost:10900, use this on the local machine:
```console
cmd:~$ kubectl -n kube-system port-forward deployment/traefik 10900:8080
```
### If K3S was installed without Traefik
To install Traefik v2 manually
```bash
helm repo add traefik https://helm.traefik.io/traefik
helm repo update
helm install traefik traefik/traefik
```
To access dashboard:
```bash
kubectl port-forward $(kubectl get pods --selector "app.kubernetes.io/name=traefik" --output=name) 9000:9000
```
then go to:
> http://localhost:9000/dashboard/

### Uninstall k3s cluster

On control nodes:
```console
bash:~$ sudo /usr/local/bin/k3s-uninstall.sh
```

On worker nodes:
```console
bash:~$ sudo /usr/local/bin/k3s-agent-uninstall.sh
```
