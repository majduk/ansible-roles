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

### Create a set of VMs to deploy openstack

```
#!/usr/bin/env ansible-playbook

# deploy infra VMs
- hosts: os-pod-control
  become: yes
  tags:
    - infra
  vars:
    default_vm:
      force_redeploy: false
      vcpu: 16
    infra_mgmt_prefix: 22
    infra_mgmt_ips:
      isidorus: 100.107.2.11
      peirce:   100.107.2.12
      racah:    100.107.2.13
    infra_oam_prefix: 23
    infra_oam_ips:
      isidorus: 172.16.148.6
      peirce:   172.16.148.7
      racah:    172.16.148.8
    gateway: 100.107.3.254
    cloud_deployer_vms:
      infra:
        password: ubuntu
        distro: ubuntu
        distro_release: bionic
        apt_http_proxy: http://100.107.0.4:1080/
        name: "{{ansible_hostname}}-infra"
        interfaces: ['brmgmt', 'broam']
        memory: "32GiB"
        disk_size: "900G"
        ip: "{{infra_mgmt_ips[ansible_hostname]}}"
        network_config: |
          version: 2
          ethernets:
            ens2:
              dhcp4: no
              addresses:
                - "{{infra_mgmt_ips[ansible_hostname]}}/{{infra_mgmt_prefix}}"
              gateway4: "{{gateway}}"
            ens3:
              dhcp4: no
          bridges:
              broam:
                interfaces: [ ens3 ] 
                addresses: 
                  - "{{infra_oam_ips[ansible_hostname]}}/{{infra_oam_prefix}}"
  roles:
    - kvm/create 

# deploy control VMs 
- hosts: os-pod-control
  become: yes
  tags:
    - control
  vars:
    default_vm:
      force_redeploy: false
      define_only: yes
      vcpu: 16
      pool: libvirtVG
      interfaces: ['broam', 'broam', 'brsac','brsac','brovr','brovr']
      memory: "32GiB"
      volumes:
        vda:
          size: 240G
        vdb:
          size: 240G
        vdc:
          size: 500G
        vdd:
          size: 500G
        vde:
          size: 500G
        vdf:
          size: 500G
      init_mode: "none"
      distro: "none"
      os:
        boot: ["network", "hd"]
    cloud_deployer_vms:
      control:
        name: "{{ansible_hostname}}-control"
      contrail:
        name: "{{ansible_hostname}}-contrail"
  roles:
    - kvm/create 

# deploy compute VMs 
- hosts: os-pod-nodes
  become: yes
  tags: 
    - compute
  vars:
    default_vm:
      force_redeploy: false
      define_only: yes
      vcpu: 16
      pool: libvirtVG
      interfaces: ['broam', 'broam', 'brsac','brsac','brovr','brovr']
      memory: "56GiB"
      volumes:
        vda:
          size: 240G
        vdb:
          size: 240G
        vdc:
          size: 500G
        vdd:
          size: 500G
        vde:
          size: 500G
        vdf:
          size: 500G
      init_mode: "none"
      distro: "none"
      os:
        boot: ["network", "hd"]
    cloud_deployer_vms:
      compute:
        name: "{{ansible_hostname}}-compute"
  roles:
    - kvm/create 

# deploy storage VMs 
- hosts: os-pod-nodes
  become: yes
  tags: storage
  vars:
    default_vm:
      force_redeploy: false
      define_only: yes
      vcpu: 16
      pool: libvirtVG
      interfaces: ['broam', 'broam', 'brsac','brsac','brsbe','brsbe']
      memory: "56GiB"
      volumes:
        vda:
          size: 240G
        vdb:
          size: 240G
        vdc:
          size: 500G
        vdd:
          size: 500G
        vde:
          size: 500G
        vdf:
          size: 500G
        vdg:
          size: 500G
        vdh:
          size: 500G
      init_mode: "none"
      distro: "none"
      os:
        boot: ["network", "hd"]
    cloud_deployer_vms:
      storage:
        name: "{{ansible_hostname}}-storage"
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
