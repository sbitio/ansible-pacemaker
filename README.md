# *This role is abandoned.*

Pacemaker
=========

Ansible role to configure Pacemaker.

Developed on CentOS 6.5 with Pacemaker version 1.1.10.

This role provides several [modules](library) to manage Pacemaker configuration
with `pcs` tool. See example below for details.

`pcs` is not available on Debian Wheezy, where `crm` is the tool available. In a
future we may add support for it.

You will likely want to use Pacemaker in conjunction with Corosync. Checkout
[sbitmedia.corosync](https://galaxy.ansible.com/list#/roles/690), and make sure
this role is included later. See example below.

Role Variables
--------------

See defaults in [`defaults/main.yml`](defaults/main.yml) for reference.

Example Playbook
----------------

```yaml
- hosts: cluster
  roles:
    - role: sbitmedia.corosync
      services:
        - service: pacemaker
          ver: 1
          user: root
          group: root
    - role: sbitmedia.pacemaker
  tasks:
    - name: Get Current DC
      shell: pcs status cluster | awk '/^ Current DC:/ {print $3}'
      register: pacemaker_dc

    - name: Set no-quorum-policy=ignore
      when: ansible_hostname == pacemaker_dc.stdout
      pcs_property: name=no-quorum-policy value=ignore

    - name: Create master virtual ip
      when: ansible_hostname == pacemaker_dc.stdout
      pcs_resource: command=create resource_id=master_vip type=ocf:heartbeat:IPaddr2
      args:
        options:
          ip: 10.0.0.20
        operations:
          - action: monitor
            options:
              interval: 10s

    - name: Master slave clone set
      when: ansible_hostname == pacemaker_dc.stdout
      pcs_resource: command=master name=ms_master_vip resource_id=master_vip
      args:
        options:
          master-max      : 1
          master-node-max : 1
          clone-max       : 2
          clone-node-max  : 1
          notify          : true
          globally-unique : false
          target-role     : Master
          is-managed      : true
```

License
-------

BSD

Author Information
------------------

Jonathan Araña Cruz - SB IT Media, S.L.

