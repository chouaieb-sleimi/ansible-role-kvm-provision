# Ansible Role: Provision KVM

Provisions KVM vms natively on RHEL/CentOS hosts.

## Requirements

Virtualization must be enabled in the host with at least 1 Virtual Network.

## Role Variables

For the whole list of variables, see `defaults/main.yml`

```yaml
# connection info
home_dir: /root
ssh_key_name: id_ed25519.pub
ssh_key: "{{ home_dir }}/.ssh/{{ ssh_key_name }}"
```

- **`home_dir`:** Home directory of the user whom the public ssh key will be injected in the VM.
- **`ssh_key_name`:** Name of the ssh public key to be injected in the VM guests.
- **`ssh_key`:** Path to the ssh public key.

```yaml
# vm spec variables
virt_user: qemu
virt_group: qemu
```

User and Group to run VMs and own their storage disks. When using a custom user, it should belong to the `libvirt` group.

```yaml
# vm spec variables
libvirt_pool_dir: /var/lib/libvirt/images
libvirt_image_dir: /var/lib/libvirt/images
base_image_name: rhel-8.7-x86_64-kvm.qcow2
vm_vcpus: 1
vm_ram_mb: 512
vm_net: default
```

- **`libvirt_pool_dir`:** points to the directory containing `qcow2` base image files.
- **`libvirt_image_dir`:** points to vm disk storage diretory. `/var/lib/libvirt/images` is the kvm defaults.
- **`vm_vcpus`:** Number of VCPUs to assign to VMs.
- **`vm_ram_mb`:** Number of mbs to give VMs.
- **`vm_net`:** KVM's Virtual Network to assign VMs to.

```yaml
# guest variables
root_pass: root
domain: example.com
vm_name: vm-rhel8.7
hostname: host_rhel8
```

- **`root_pass`:** Password for the `root` user
- **`domain`:** Used to define VMs' FQDN
- **`vm_name`:** VM name visible in the `virsh list` command
- **`hostname`:** hostname of each VM

Customization role variables example:

```yaml
# connection info
home_dir: "{{ lookup('ansible.builtin.env', 'HOME') }}"
ssh_key_name: id_ed25519.pub
ssh_key: "{{ home_dir }}/.ssh/{{ ssh_key_name }}"

# vm spec variables
virt_user: "{{ lookup('ansible.builtin.env', 'USER') }}"
virt_group: "{{ lookup('ansible.builtin.env', 'USER') }}"
libvirt_pool_dir: "/var/lib/libvirt/images"
libvirt_image_dir: "/var/lib/libvirt/images"
base_image_name: "rhel-8.7-x86_64-kvm.qcow2"
vm_vcpus: 1
vm_ram_mb: 2048
vm_net: "network_labs"

# guest variables
root_pass: "root"
domain: "ansible.labs"
vm_name: "control_rhel8.7"
hostname: "control"
```

---

## Dependencies

Only `ansible` is needed to run this role.

## Example Playbook

You can use this role to easily provision a list of VMs with a great deal of control over the specs of each VM. You only need to customize the `vm_list` list variable before looping the role over it. example:

```yaml
---
- name: Provision KVM hosts
  hosts: localhost
  become: true
  vars:
    vm_list:
      - {
          vm_name: "control_rhel8.7",
          hostname: "control",
          base_image_name: "rhel-8.7-x86_64-kvm.qcow2",
        }
      - {
          vm_name: "node-1_alma8.7",
          hostname: "node1",
          base_image_name: "AlmaLinux-8-GenericCloud-8.7.x86_64.qcow2",
        }
      - {
          vm_name: "node-2_alma8.7",
          hostname: "node2",
          base_image_name: "AlmaLinux-8-GenericCloud-8.7.x86_64.qcow2",
        }
      - {
          vm_name: "node-3_alma8.7",
          hostname: "node3",
          base_image_name: "AlmaLinux-8-GenericCloud-8.7.x86_64.qcow2",
        }
      - {
          vm_name: "node-4_alma8.7",
          hostname: "node4",
          base_image_name: "AlmaLinux-8-GenericCloud-8.7.x86_64.qcow2",
        }

  tasks:
    - name: Include role kvm_provision
      ansible.builtin.include_role:
        name: kvm_provision
      vars:
        vm_name: "{{ item.vm_name }}"
        hostname: "{{ item.hostname }}"
        base_image_name: "{{ item.base_image_name }}"
      loop: "{{ vm_list }}"
```

## License

BSD
