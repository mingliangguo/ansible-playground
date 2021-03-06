# Overview

## Introduction

The ansible playbook helps you create and configure a base vm environment. So you can connect this playbook with your normal dockerBuild task and it helps automate the rest of steps to tag/deploy the image.

## Prerequisites

Make sure you have `ansible` installed, and you are connected to p2 environment, and the `kubectl` works for deployment updates.

```bash
brew install ansible # for Mac
```

## Usage

```
## Clone the repo
git clone git@github.com:mingliangguo/ansible-playground.git

# add an alias to your .bashrc / .zshrc 
alias cd $YOUR_CLONE_FOLDER && ansible-playbook -i inventory.ini deploy-kube-service.yml
```

## Install custom roles

Use 
```bash
ansible-galaxy install -r requirements.yam
```

or install them individually:

```bash
ansible-galaxy install nover.ohmyzsh
ansible-galaxy install geerlingguy.nodejs
ansible-galaxy install andrewrothstein.yarn
ansible-galaxy install geerlingguy.docker
ansible-galaxy install andrewrothstein.kind
```

See how the role is used in `configure-vm.yml`

```yml
  roles:
    - { role: nover.ohmyzsh, ohmyzsh_theme: 'robbyrussell', become: true }
```

## Configure your VM


1. Create a VM using vagrant (or any other tool to create a base vm)
2. Create your own user (better not using root directly)

```bash
adduser your_user_name
ssh-copy-id -i path/to/certificate username@remote_host
```

3. Enable sudo without passwordPermalink

```bash
usermod -aG sudo username
```

Then configure password-less sudo.

Edit `/etc/sudoers` file and add a line as below for the user you want to allow nopassword sudo.

Refer to:
- [how to create a sudo user in ubuntu](https://linuxize.com/post/how-to-create-a-sudo-user-on-ubuntu/)
- [sudo without password](https://linuxhandbook.com/sudo-without-password/)


```bash
sudo cp /etc/sudoers ~/sudoers.bak
sudo visudo
# Add the following line to the sudoers file
user_name ALL=(ALL) NOPASSWD:ALL
```

The steps above can actually be achieved via the following playbook:

Once you get the vm setup, you can add the following entry to `sandbox.ini`

```ini
vm-host ansible_user=root ansible_host=vm-host
```

Then use `create-user.yaml` playbook to setup a non-root user account on the vm, and it will ask you the password of your root account:

```bash
ansible-playbook -vvv -i sandbox.ini create-user.yaml -e host_name=vm-host -e user_name=gary -e user_password=your_password --ask-pass  
```

And after that, you can add the host to `sandbox.ini` and run the next playbook to setup other software.

```bash
ansible-playbook -i sandbox.ini configure-vm.yml -e host_name=gary-dev1 -e user_name=gary
```

*Note:* The detail information about the `gary-dev1` host is in the `sandbox.ini` file. Make sure password-less ssh and sudo are enabled on the host.
