oVirt cluster upgrade
=========

This role will iterate through all hosts in cluster and upgrades them.

Requirements
------------

 * Ansible version 2.3 or higher
 * Python SDK version 4 or higher

Role Variables
--------------

| Name               | Default value         |                            |
|--------------------|-----------------------|----------------------------| 
| cluster            | Default               | Name of the cluster to be upgraded |
| low_prio_vms       | UNDEF                 | List of VM names to be stopped before upgrade |
| force_pinned       | false                 | If true the pinned to host VMs will be stopped |

Dependencies
------------

No.

Example Playbook
----------------

```yaml
---
- name: oVirt infra
  hosts: localhost
  connection: local
  gather_facts: false

  vars:
    engine_url: https://ovirt-engine.example.com/ovirt-engine/api
    engine_user: admin@internal
    engine_password: 123456
    engine_cafile: /home/omachace/ovirt-engine41/etc/pki/ovirt-engine/ca.pem

    cluster_name: production
    low_prio_vms:
      - openshift_master_0
      - openshift_node_0
      - openshift-node-image

  roles:
    - ovirt-cluster-upgrade
```

[![asciicast](https://asciinema.org/a/111757.png)](https://asciinema.org/a/111757)

License
-------

Apache License 2.0
