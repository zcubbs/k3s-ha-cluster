# Ansible Playbooks to deploy base apps to Kubernetes

##### Prerequesites

If ansible is not installed, refer to [Install Ansible on WSL](../wsl/README.md).

Install helm:
```bash
curl https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3 | bash
```
Also, install these first:
- Python 3
- Pip 3

```bash
/usr/bin/python3 -m pip install --upgrade --user openshift
~~/usr/bin/python3 -m pip install --upgrade --user helm~~ (this may cause conflicts if you already have a helm install)
```
Install helm as recommended on https://helm.sh/docs/intro/install
(On Ubuntu WSL better use the apt-get install)

#### Install apps
One liner
```bash
sudo ansible-playbook install.yaml 
```

selective
```bash
sudo ansible-playbook install.yaml --extra-vars "apps=longhorn"
```

