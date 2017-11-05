# My Ansible Playground

This repository is used to collect the frequently used playbooks I created to automate some of my routine work.


## Usage

Make sure you have `ansible` installed and configure the host in `inventory.ini`

### Configure a remote server 

It includes:

- setup [byobu]( http://byobu.co/ )
- install unzip, curl, bash-completion
- configure a minimal vim setting with nerdtree and ctrlp
- install and configure fzf
- enable auto-completion for `kubectl`

```bash
ansible-playbook -i inventory.ini playbooks/configure-vm.yml -e host_name=myserver
```

> **Note**:
There are two other playbooks, one runs a nodejs application in a remote docker container, the other fetchs the logs and generated report to local.
