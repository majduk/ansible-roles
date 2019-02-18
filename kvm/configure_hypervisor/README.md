# Configure host as a Libvirt hypervisor

Example of playbook using this module:

```
- hosts: host1
  gather_facts: False
  become: true
  tasks:
  - name: install python 2
    raw: test -e /usr/bin/python || (apt -y update && apt install -y python-minimal)

- hosts: host1
  become: true
  roles:
    - kvm/configure_hypervisor
```

