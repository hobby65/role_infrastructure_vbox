# infrastructure_vbox role

This role will create a vagrant VM on Oracle Virtualbox from a defenition
in the inventory.
The inventory needs to be constructed in the format this role wants the vars,
which will be explained below.

## Group_vars

The group_vars for the vbox role to work, is as followes:

````yml
vbox:
  server: virtserver.localdomain
  networks:
    backend1:
      netmask: 255.255.255.0
      network: 10.0.10.0
      net_name: NatNetwork
    frontend1:
      gateway: 192.168.15.1
      netmask: 255.255.255.0
      network: 192.168.15.0
      net_name: natnet1
  vm_folder: 'F:'
  vm_group: datacenter
  template_box: redhat/rhel-8
````

First is the 'server' in the vbox section, this is the inventory name of the 
machine that runs the actual vbox instance.
The networks defeintions are there for the templating of the vm and hold the
network names as created in vbox.
* vm_folder is the drive or path your VM's will be created in
* vm_group is the groupname you want your VM's to be created in in vbox
* template_box is the 'box' to use as the template for the VM

## host_vars

The host_vars to define a VM are rather simple, because most of the common
facts are hidden elsewhere in the inventory as group_vars.

````yml
hostname: testhost1.localdomain
disks:
  - vg: test
    size: 10
lvm:
  - lv_name: test
    mountpoint: /opt/test
    size: '10'
    vg_name: test
os:
  name: rhel
  version: 1.0.0
primary_interface:
  ip: 192.168.15.100
  network: frontend1
size: s
type: vm
````

The above example will create a host on vbox with the name testhost1.localdomain
and with an extra disk attached to it of 10GB with a physical volume name of 'test'.
This role will do no further configuration on this VM, this should be done by other
plays.
Be aware that the 'vg' names of the disks need to be unique, vbox doesn't handle
multiple names (eg. disk1 and disk2) you will get an error creating the disks.
This is vbox(bug) related, not a play or template issue.
