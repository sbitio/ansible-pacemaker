Pacemaker
=========

Ansible role to configure Pacemaker.

This role has been developed on CentOS 6 on Pacemaker version 1.1.10.

Manage both CentOS and Debian packages. At present, it only manages pacemaker
configuration in CentOS (by using `pcs`). In a future we will add support for
Debian Wheezy (where `crm` is the only tool available at this time).

Role Variables
--------------

See defaults in [`defaults/main.yml`](defaults/main.yml) for reference.

Example Playbook
----------------

You will likely want to use pacemaker in conjunction with corosync. Checkout
[sbitmedia.corosync](https://galaxy.ansible.com/list#/roles/690), and make sure
this role is included later.

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
```

Btw there're plans to add support for [cman](http://www.sourceware.org/cluster/cman/) in a future.

License
-------

BSD

Author Information
------------------

Jonathan Araña Cruz - SB IT Media, S.L.

