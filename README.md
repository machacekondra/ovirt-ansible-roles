# ovirt-ansible-roles

Ansible roles to build oVirt infrastracture.

## Implemented roles

* [ovirt-aaa-jdbc]
* [ovirt-clusters]
* [ovirt-datacenters]
* [ovirt-hosts]
* [ovirt-networks]
* [ovirt-permissions]
* [ovirt-storages]

## Example

Playbook example

```yaml
- hosts: engine

  vars_files:
    ovirt-infra-vars.yml
    
  pre_tasks:
    - name: Login to oVirt
      ovirt_auth:
        url: "{{ engine_url }}"
        username: "{{ engine_user }}"
        password: "{{ engine_password }}"
        ca_file: "{{ engine_cafile | default(omit) }}"
        insecure: "{{ engine_insecure | default(true) }}"
      tags:
        - always

  roles:
    - ovirt-datacenters
    - ovirt-clusters
    - ovirt-hosts
    - ovirt-networks
    - ovirt-storages
    - ovirt-aaa-jdbc
    - ovirt-permissions

  post_tasks:
    - name: Logout from oVirt
      ovirt_auth:
        state: absent
        ovirt_auth: "{{ ovirt_auth }}"
      tags:
        - always 
```

Variables example

```yaml
---
###########################
# REST API variables
###########################
engine_url: https://engine/ovirt-engine/api
engine_user: admin@internal
engine_password: 123
engine_insecure: true

###########################
# Common
###########################
compatibility_version: 4.2

# Data center
data_center_name: mydatacenter

###########################
# Clusters
###########################
clusters:
 - name: development
   cpu_type: Intel Conroe Family
   description: Development cluster
   ballooning: true
   ksm: true
   host_reason: false
   vm_reason: false
   memory_policy: server
   migration_policy: post_copy
 - name: production
   cpu_type: Intel Conroe Family
   description: Production cluster
   ballooning: false
   ksm: false
   host_reason: true
   vm_reason: true
   memory_policy: disabled
   migration_policy: suspend_workload
   scheduling_policy: evenly_distributed
   ha_reservation: true
   fence_enabled: true
   fence_skip_if_connectivity_broken: true
   fence_skip_if_sd_active: true

###########################
# Hosts
###########################
hosts:
  - name: myhost1
    address: 192.168.200.2
    cluster: development
    password: 123456
  - name: myhost2
    address: 192.168.200.4
    cluster: production
    password: 123456

###########################
# Storage
###########################
storages:
  nfs:
    master: true
    state: present
    nfs:
      address: 192.168.200.3
      path: /exports/nfs/share1
  iscsi:
    state: present
    iscsi:
      target: iqn.2014-07.org.ovirt:storage
      port: 3260
      address: 192.168.200.3
      username: username
      password: password
      lun_id:
        - 36001405fb8d5778ba924c428385cc30f
  templates:
    domain_function: export
    nfs:
      address: 192.168.200.3
      path: /exports/nfs/exported
  iso:
    domain_function: iso
    nfs:
      address: 192.168.200.3
      path: /exports/nfs/iso

###########################
# Networks
###########################
logical_networks:
  - name: mynetwork
    clusters:
      - name: development
        assigned: yes
        required: no
        display: no
        migration: yes
        gluster: no

host_networks:
  - name: myhost1
    check: true
    save: true
    bond:
      name: bond0
      mode: 2
      interfaces:
        - eth2
        - eth3
    networks:
      - name: mynetwork
        boot_protocol: dhcp

###########################
# Users & Groups
###########################
aaa_profile_type: 389ds
aaa_user: uid=user1,ou=People,dc=lago,dc=local
aaa_password: 123456
aaa_profile_name: testldap
aaa_ldap:
  - 192.168.200.3

users:
  - name: user1
    authz_name: internal-authz
    password: 1234568
    valid_to: "2018-01-01 00:00:00Z"
  - name: user2
    authz_name: internal-authz
    password: 1234568
    valid_to: "2018-01-01 00:00:00Z"

user_groups:
  - name: group1
    authz_name: internal-authz
    users:
      - user1

permissions:
  - state: present
    user_name: user1
    authz_name: internal-authz
    role: UserRole
    object_type: cluster
    object_name: development

  - state: present
    group_name: group1
    authz_name: internal-authz
    role: UserRole
    object_type: cluster
    object_name: production
```

[ovirt-aaa-jdbc]: https://github.com/machacekondra/ovirt-ansible-roles/blob/master/roles/ovirt-aaa-jdbc/README.md
[ovirt-clusters]: https://github.com/machacekondra/ovirt-ansible-roles/blob/master/roles/ovirt-aaa-clusters/README.md
[ovirt-datacenters]: https://github.com/machacekondra/ovirt-ansible-roles/blob/master/roles/ovirt-aaa-datacenters/README.md
[ovirt-hosts]: https://github.com/machacekondra/ovirt-ansible-roles/blob/master/roles/ovirt-aaa-hosts/README.md
[ovirt-networks]: https://github.com/machacekondra/ovirt-ansible-roles/blob/master/roles/ovirt-aaa-networks/README.md
[ovirt-permissions]: https://github.com/machacekondra/ovirt-ansible-roles/blob/master/roles/ovirt-aaa-permissions/README.md
[ovirt-storages]: https://github.com/machacekondra/ovirt-ansible-roles/blob/master/roles/ovirt-aaa-storages/README.md
