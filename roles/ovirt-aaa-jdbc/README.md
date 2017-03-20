Add internal users/groups to oVirt
==================================

This role manages the internal users and groups in oVirt.

Requirements
------------

None


Role Variables
--------------

```yaml
---
users: List of dictionaries that describe the users to be added.
       The dictionary can contain following values:
         - state: present/absent
         - name: name of the user
         - authz_name: name of the authorization provider where user belongs
         - password: the password of the user
         - valid_to: password expiration date
user_groups: List of dictionaries that describe the groups to be added.
             The dictionary can contain following values:
               - state: present/absent
               - name: name of the group
               - authz_name: name of the authorization provider where group belongs
               - users: list of user names, which group should have
```

Dependencies
------------

None


Example Playbook
----------------

```yaml
---
hosts: engine
  vars:
    users:
      - name: user1
        authz_name: internal-authz
        password: 123456
        valid_to: "2025-01-01 00:00:00Z"
      - name: user2
        authz_name: internal-authz
        password: 123456
        valid_to: "2025-01-01 00:00:00Z"
    
    user_groups:
      - name: group1
        authz_name: internal-authz
        users:
          - user1
   
  roles:
    - ovirt-aaa-jdbc
```

License
-------
BSD

Author Information
------------------

Ondra Machacek (@machacekondra)
