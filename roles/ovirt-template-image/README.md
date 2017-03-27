Role Name
=========

This role downloads Manage IQ virtual appliance and deploys it into oVirt.
This role is customizable so it may work with other appliances as well, but the main goal is Manage IQ.

Requirements
------------

oVirt has to be 4.0.4 or higher and [ovirt-imageio](http://www.ovirt.org/develop/release-management/features/storage/image-upload/) must be installed and running.
The target machine has to have installed [Python SDK](https://pypi.python.org/pypi/ovirt-engine-sdk-python/4.0.4).

Check `ovirt-imageio-proxy` is running on engine:

```
systemct status ovirt-imageio-proxy
```

Also check if `ovirt-imageio-daemon` is running on hosts:

```
systemct status ovirt-imageio-daemon
```

You also have to use your CA certificate, you can configure the path with `ovirt_ca` variable.

Role Variables
--------------

OVA varibles:
```
 - ova_url - Define the URL where the Manage IQ OVA is stored (default: http://releases.manageiq.org/manageiq-ovirt-euwe-1.ova).
```

oVirt engine login variable:
```
 - ovirt_user - Define the user to be used to access oVirt (default: admin@internal).
 - ovirt_password - Define password of the 'ovirt_user'.
 - ovirt_url - Define the URL of the oVirt.
 - ovirt_ca - Define the path to the CA of the oVirt (default: /etc/pki/ovirt-engine/ca.pem).
```

Virtual Machine variables:
```
 - vm_name - Define the name of the Manage IQ VM in oVirt (default: manageiq).
 - vm_cluster - Define the cluster of the Manage IQ VM in oVirt (default: Default).
 - vm_cloud_init - Define cloud init dictionary to be passed to Manage IQ VM in oVirt (By default set root password to 'password').
```

Virtual Machine disks variables:
```
 - disk_storage_domain: Define the target storage domain of the disk (default: data).
 - disk_size: Define the size of the disk (default: 50GiB).
```

Virtual Machine nics variables:
```
 - vm_nics: Dictionary variable which define network interfaces for vm (By default create one nic in 'ovirtmgmt' network).
 - vm_cloud_init_nics: Dictionary variable which define network interfaces for cloud init. (By default create eth0 dhcp starting on boot NIC).
```

Dependencies
------------

No.

Example Playbook
----------------

Note that for passwords you should be using Ansible vault.

```yaml
    - name: Deploy ManageIQ to oVirt engine
      hosts: myovirt
      gather_facts: no
      vars:
        ovirt_url: https://myovirt/ovirt-engine/api
        ovirt_password: 123456

      roles:
        - machacekondra.ovirt-manageiq
```

Customizing the VM and disk:

```yaml
    - name: Deploy ManageIQ to oVirt engine
      hosts: myovirt
      gather_facts: no
      vars:
        ovirt_url: https://myovirt/ovirt-engine/api
        ovirt_password: 123456
        vm_name: manageiq
        vm_cluster: mycluster
        vm_memory: 4GiB
        vm_cpu: 1
        disk_storage_domain: mydata
        disk_size: 100GiB
        vm_cloud_init:
          user_name: root
          root_password: securepassword
        vm_nics:
          - name: eth0
            profile_name: thenetwork
            interface: virtio

      roles:
        - machacekondra.ovirt-manageiq
```

This role is using `ovirt_vms` and `ovirt_disks` modules, for more info please take a look [here](http://docs.ansible.com/ansible/list_of_cloud_modules.html#ovirt).

License
-------

GPLv2
