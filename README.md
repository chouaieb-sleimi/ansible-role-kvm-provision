# kvm_provision

an Ansible role that provisions the guest vms defined in the variable `vm_list` and ensures the most basic guest connectivity steps.

**Role features:**

- Provision vm guests
- Adds provisioned vms hostnames to host's `/etc/hosts` file
- Update `known_hosts` file (to avoid ssh key fingerprint connection errors)
