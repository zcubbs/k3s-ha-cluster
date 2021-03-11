# WSL Setup
## Install ansible on WSL

```bash
sudo apt remove ansible
sudo python3 get-pip.py
sudo python3 -m pip install ansible
ansible --version
```

# Setup wsl ssh profiles to Nodes

### On each node:
Ansible user
```bash
sudo adduser <ANSIBLE_USER>
sudo usermod -aG sudo <ANSIBLE_USER>
sudo su - <ANSIBLE_USER>
ssh-keygen 
```
then edit config to enable password-less connection:
```bash
sudo visudo
```
and add line at bottom:
```text
<ANSIBLE_USER> ALL=(ALL) NOPASSWD: ALL
```

### To remove an old ssh key
```cmd
ssh-keygen -R <ip>
```
### On Windows machine:
For WSL
```bash
ssh-keygen 

ssh-copy-id -i ~/.ssh/id_rsa <ANSIBLE_USER>@<HOSTNAME_CONTROL_1>
ssh-copy-id -i ~/.ssh/id_rsa <ANSIBLE_USER>@<HOSTNAME_WORKER_1>
etc..
```
For windows powershell
```powershell
scp $HOME\.ssh\id_rsa.pub <ANSIBLE_USER>@<HOSTNAME_CONTROL_1>:~/.ssh/authorized_keys
scp $HOME\.ssh\id_rsa.pub <ANSIBLE_USER>@<HOSTNAME_WORKER_1>:~/.ssh/authorized_keys
etc...
```

Then update Windows Terminal config by adding these profiles:
```json
[
    {
      "guid": "{45959395-e176-46d2-afd1-48030579c7d6}",
      "hidden": false,
      "name": "💻 K3S - Control-1",
      "tabTitle": "💻 K3S - Control-1",
      "icon": "⚡",
      "suppressApplicationTitle": true,
      "colorScheme": "Tango Dark",
      "commandline": "ssh <ANSIBLE_USER>@<HOSTNAME>"
    },
    {
    "guid": "{20fe2a4e-640a-4e1b-8a0d-3e197c23453c}",
    "hidden": false,
    "name": "💻 K3S - Worker-1",
    "tabTitle": "💻 K3S - Worker-1",
    "icon": "⚡",
    "suppressApplicationTitle": true,
    "colorScheme": "Tango Dark",
    "commandline": "ssh <ANSIBLE_USER>@<HOSTNAME>"
    }
]

etc...
```

Install zsh and oh-my-zsh:
```bash
sudo apt-get install zsh
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

OR just install zsh4humans for a better experience
```shell
sh -c "$(curl -fsSL https://raw.githubusercontent.com/romkatv/zsh4humans/v4/install)"
```

On WSL add the following to .ssh/config for ssh auto complete
```shell
Host worker-1
User <ANSIBLE_USER>
Port 22
Hostname <HOSTNAME>

Host control-1
User <ANSIBLE_USER>
Port 22
Hostname <HOSTNAME>

etc..
```
