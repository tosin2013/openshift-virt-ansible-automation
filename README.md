# OpenShift Virtualization Ansible Automation

This repository contains a set of Ansible playbooks and roles to automate the deployment and management of virtual machines (VMs) on OpenShift Virtualization. The automation leverages the Ansible Kubernetes and KubeVirt modules to interact with the OpenShift and KubeVirt APIs.

## Purpose

The main purpose of this repository is to provide a collection of Ansible automation resources that enable efficient, consistent, and repeatable provisioning and management of VMs on OpenShift Virtualization. By using Ansible, you can define your VM infrastructure as code and manage it through a GitOps workflow.

## Features

- Deployment of the OpenShift Virtualization operator
- Creation and management of VM instances
- Provisioning of VMs from templates
- Attachment of storage to VMs
- Integration with OpenShift GitOps (ArgoCD) for continuous deployment

## Prerequisites

- An OpenShift cluster with OpenShift Virtualization enabled
- Ansible 2.9 or later
- Kubernetes Python module
- KubeVirt Python module

## Usage

1. Clone this repository:
   ```
   git clone https://github.com/your-username/openshift-virt-ansible-automation.git
   ```

2. Install the required Ansible dependencies:
   ```
   ansible-galaxy collection install -r requirements.yml
   ```

3. Update the `inventory` file with your OpenShift cluster details.

4. Run the desired Ansible playbook:
   ```
   ansible-playbook playbooks/deploy-vm.yml
   ```

## Playbooks

- `playbooks/deploy-operator.yml`: Deploys the OpenShift Virtualization operator
- `playbooks/create-vm.yml`: Creates a new VM instance
- `playbooks/provision-vm-from-template.yml`: Provisions a VM from a template
- `playbooks/attach-storage.yml`: Attaches storage to a VM

## Roles

- `roles/openshift-virtualization-operator`: Installs and configures the OpenShift Virtualization operator
- `roles/vm-instance`: Creates and manages VM instances
- `roles/vm-template`: Manages VM templates
- `roles/vm-storage`: Configures and attaches storage to VMs

## Contributing

Contributions are welcome! If you find any issues or have suggestions for improvements, please open an issue or submit a pull request.

## License

This project is licensed under the [MIT License](LICENSE).

## References

- [OpenShift Virtualization Documentation](https://docs.openshift.com/container-platform/latest/virt/about-virt.html)
- [Ansible Kubernetes Module](https://docs.ansible.com/ansible/latest/collections/kubernetes/core/k8s_module.html)
- [Ansible KubeVirt Module](https://docs.ansible.com/ansible/2.9/modules/kubevirt_vm_module.html)
