# KVM VM create

## Examples

### Create 2 VMs with IPs
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
        distro: ubuntu
        distro_release: bionic
        interfaces: ['br-dev']
        ip: 10.1.0.100
      vm2:
        name: "test-2"
        distro: centos
        distro_release: centos7
        interfaces: ['br-dev']
        ip: 10.1.0.101
  roles:
    - kvm/create
```

### Create a set of VMs with multiple disks

```
#!/usr/bin/env ansible-playbook

- hosts: reeves
  become: yes
  vars:
    default_vm:
      define_only: yes
      vcpu: 16
      memory: "16384MiB"
      pool: libvirtVG
      volumes:
        vda:
          size: 10G
        vdb:
          size: 10G
        vdc:
          size: 10G
        vdd:
          size: 10G
        vde:
          size: 10G
      interfaces: ['br-oam', 'br-prod', 'br-back']
      init_mode: "none"
      distro: "none"
      os:
        boot: ["network", "hd"]
    cloud_deployer_vms:
      vm1:
        name: "reeves-vm1"
      vm2:
        name: "reeves-vm2"
      vm3:
        name: "reeves-vm3"
      vm4:
        name: "reeves-vm4"
      vm5:
        name: "reeves-vm5"
      vm6:
        name: "reeves-vm6"
      vm7:
        name: "reeves-vm7"
      vm8:
        name: "reeves-vm8"
      vm9:
        name: "reeves-vm9"
  roles:
    - kvm/create
```

## Parameters

Global parameters (with defaults):
- cd_apt_http_proxy - global APT proxy
- cd_gateway - global IPv4 gateway
- cd_netmask - global IPv4 netmask

VM parameters (with defaults):
- force_redeploy: false - force VM redeployment if it already exists?
- distro: null/none ubuntu centos
- distro_release: trusty/xenial/bionic/centos7
- ip: none or IP
- memory: "4096MiB"
- vcpu: 4
- disk_size: "20G"
- interfaces - which hypervisor bridges attach VM to
- ssh_user_keys
- define_only: no - only define a VM or define and start
- pool - which storage pool should be used
- volumes - list of volumes with sizes the VM should have
