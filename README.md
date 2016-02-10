nsible-libvirt
Ansible module for libvirt api

# Modules
we are currently providing :-
* virtinstall  for provision new virtual machines 
* qemu-img for QEMU disk image utility 
* 

# Documentation 

#### module: virtinstall
###### description: Launch the VMs on OVS switch with portgroup

| Field          | required       | description  |
| :------------- |:-------------- | :------------|
| name           | true      | Name of the Virtual Machine |
| ram            | true      | Physical memory |
| vcpus          | true      | Number of Virtual CPUs |
| disk           | true      | disk information |
| network        | true      | Required networks (input as dict with portgroup) | 
| virt_type      | false     | type of hypervisior default is "kvm" |
| os_type        | false     | operating system type default linux |
| pxe            | false     | Bootstrap with pxe server | choices: [ "True", "False" ] |
| import_image   | false     | launch vm from image default value is no | 
| force          | false     | If yes, allows to create new VM and override the old VM choices: [ "yes", "no" ] |


#### module: qemu-img
###### description: This module creates raw and volume storage disk

| Field          | required       | description  |
| :------------- |:-------------- | :------------|
| name           | -         | Name of volume(Required when disk_type is volume) |
| dest           | -         | path of raw disk( Required when disk_type is raw) |
| format         | true      | format of image(img/qcow2)|
| disk_type      | true      | type of disk(raw or volume) |
| pool           | -         | pool name(required if disk_type is volume) | 
| src_img        | -         | path of the source image( Required when state=convert) |
| dest_img       | -         | path of the destination image( Required when state=convert) |
| size           | true      | size of the disk and volume |
| state          | true      | choices=['present','convert', 'absent'] | 
| force          | false     | allows to create new disk and volume to override already exists choices: [ "yes", "no" ] |

# example
###### Create a new VM with bootstrap from PXE.
```yaml
- virtinstall:
    name: test1
    ram: 1024
    vcpus: 2
    virt_type: kvm
    pxe: True
    disk: ['/var/lib/libvirt/images/osdisk.img','/var/lib/libvirt/images/appdisk.img']
    network:
      pxe_interface:
        bridge: virbr0
        portgroup: br0
```

######## Create a new VM from an existing Image
```yaml
- virtinstall:
    name: test1
    ram: 1024
    vcpus: 2
    virt_type: kvm
    disk: ['/var/lib/libvirt/images/osdisk.img','/var/lib/libvirt/images/appdisk.img']
    network:
      pxe_interface:
        bridge: virbr0
        portgroup: br0
    image_import: True
```

###### Create a raw disk 
```yaml
- qemu-img: dest=/var/lib/libvirt/images/test.img disk_type=raw  size=8G state=present force=yes 
```

######## Create a volume 
```yaml  
- qemu-img: name=testing.qcow2 disk_type=volume  size=8G state=present force=yes 
```

######## Convert image
```yaml  
- qemu-img: src_img=/var/lib/libvirt/images/test5.img dest_img=/var/lib/libvirt/images/test5.qcow2 disk_type=qcow2 state=convert 
```

######## Delete Raw Disk
```yaml  
- qemu-img: dest=/var/lib/libvirt/images/test4.img  disk_type=raw state=absent 
```

######## Delete Volume Disk
```yaml  
- qemu-img:  name=test.qcow2  disk_type=volume state=absent 
```
