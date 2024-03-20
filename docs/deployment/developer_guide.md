---
layout: default
title: Developers Guide
parent: Deployment Documenation 
nav_order: 1
---

## Getting Started


## Requirements 
* Install Ansible Navigator
* Optional `mkdir -p /home/${USER}/.run/container`

**Git Clone Repo**
```
git clone https://github.com/tosin2013/openshift-virt-ansible-automation.git

cd $HOME/openshift-virt-ansible-automation/
```

**Configure SSH**
```
IP_ADDRESS=$(hostname -I | awk '{print $1}') # confirm it is the ip address of the control node
ssh-keygen -f ~/.ssh/id_rsa -t rsa -N ''
ssh-copy-id $USER@${IP_ADDRESS}
```

**Create Ansible navigator config file**
```
# export INVENTORY=dev
# cp -avi inventories/sample/* inventories/${INVENTORY}
# cat >~/.ansible-navigator.yml<<EOF
---
ansible-navigator:
  ansible:
    inventory:
      entries:
      - /home/$USER/openshift-virt-ansible-automation/inventories/${INVENTORY}
  execution-environment:
    container-engine: podman
    enabled: true
    environment-variables:
      pass:
      - USER
    image:  localhost/openshift-virt-ansible-automation:0.1.0
    pull:
      policy: missing
  logging:
    append: true
    file: /home/$USER/openshift-virt-ansible-automation/ansible-navigator.log
    level: debug
  playbook-artifact:
    enable: false
EOF
```

**Add hosts file**
```
# control_user=${USER}
# control_host=$(hostname -I | awk '{print $1}') # confirm it is the ip address of the control node
echo "[control]" > inventories/${INVENTORY}/hosts
echo "control ansible_host=${control_host} ansible_user=${control_user}  ansible_python_interpreter=/usr/bin/python3" >> inventories/${INVENTORY}/hosts
# cat inventories/${INVENTORY}/hosts
```

**Create Requirement file for ansible builder**   
Red Hat KVM Role [redhat.openshift_virtualization:](https://console.redhat.com/ansible/automation-hub/repo/published/redhat/openshift_virtualization/docs/)
```
cat >ansible-builder/requirements.yml<<EOF
---
collections:
  - ansible.posix
  - containers.podman
  - kubernetes.core
  - kubevirt.core # change to redhat.openshift_virtualization is using Ansible Automation Hub
  - name: tosin2013.openshift_virt_ansible
    type: git
    source: https://github.com/tosin2013/openshift_virt_ansible.git
    version: main
roles: 
  - linux-system-roles.network
  - linux-system-roles.firewall
  - linux-system-roles.cockpit
EOF
```


**Build the image:**
```bash
make build-image
```

**Configure Ansible Vault**
```bash
curl -OL https://gist.githubusercontent.com/tosin2013/022841d90216df8617244ab6d6aceaf8/raw/92400b9e459351d204feb67b985c08df6477d7fa/ansible_vault_setup.sh
chmod +x ansible_vault_setup.sh
./ansible_vault_setup.sh
```

**List inventory**
```
ansible-navigator inventory --list --inventory=inventories/dev -m stdout --vault-password-file $HOME/.vault_password
```

**Install OpenShift CLI**
```
curl -OL https://raw.githubusercontent.com/tosin2013/openshift-4-deployment-notes/master/pre-steps/configure-openshift-packages.sh
chmod +x configure-openshift-packages.sh
./configure-openshift-packages.sh -i
```

**Deploy Openshift  Virtualization**
```
$ ssh-agent bash
$ ssh-add ~/.ssh/id_rsa
$ oc login --token=sha256~token --server=https://api.lab.example.com:6443
$ ansible-navigator run --inventory=inventories/dev ansible-navigator/setup_openshift_virt.yml \
 --vault-password-file $HOME/.vault_password -m stdout 
```

When developing a new collection, you can use the following command to build the collection and install it in the execution environment:
```
make build-image
```

When you are done developing, you can remove the images and bad builds with the following commands:
```
make remove-bad-builds
make remove-images
```
