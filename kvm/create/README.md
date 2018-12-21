# KVM VM create

Example of playbook using this module:

```
#!/usr/bin/env ansible-playbook

- hosts: hypervisor
  become: yes
  vars:
    default_vm:
      apt_http_proxy: "http://10.1.0.1:8080/"
      gateway: "10.1.0.1"
      netmask: "24"
    cloud_deployer_vms:
      vm1:
        name: "test-1"
        distro_release: bionic
        interfaces: ['br-dev']
        ip: 10.1.0.100
      vm2:
        name: "test-2"
        distro_release: centos7
        interfaces: ['br-dev']
        ip: 10.1.0.101
  roles:
    - kvm/create
```

Global parameters (with defaults):
- cloud_deployer_force: no - redeploy running VMs or keep them
- cd_apt_http_proxy - global APT proxy
- cd_gateway - global IPv4 gateway
- cd_netmask - global IPv4 netmask

VM parameters (with defaults):
- memory: "4096MiB"
- vcpu: 4
- disk_size: "20G"
- interfaces - which hypervisor bridges attach VM to
- ssh_user_keys
- define_only: no - only define a VM or define and start
