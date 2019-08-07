# Download a set of cloud images

Role handy to set up a local cloud image repository to use eg. with libvirt. Images are downloaded by default to `/var/lib/libvirt/images` and converted to qcow2 format.

Example usage:
```
- hosts: os-pod-all
  become: yes
  roles:
    - role: get_cloud_image
      cd_images_distro_cloud_image_url: "https://cloud-images.ubuntu.com/bionic/current/bionic-server-cloudimg-amd64.img"
      cd_distro_release: bionic
      cd_check_hash_and_signature: True
```
