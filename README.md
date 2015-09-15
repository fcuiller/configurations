# configurations

1 - finish LLD to get final configuration template

2 - identify device roles (core, tor box, tor cache, tor admin, tor )

3 - for each role, identify the variables and build the template:

cisco@ansible:~/nxos-ansible/templates$ cat core.j2
hostname {{ inventory_hostname }}

interface mgmt0
  ip address {{ mgmt_ip }}/24

vlan 1337
  name FOOBAR

username cisco password cisco

4 - create the device file var with all specific info (IP, remote AS, BGP, etc.):
cisco@ansible:~/nxos-ansible/host_vars$ cat SW-IP3-SL-ORANGE-65001-1.yml
---
mgmt_ip: 172.17.255.88

5 - deploy the configuration

5a - use PoAP + this generated file to autoprovision
no need to configure an IP management before, pretty cool. use DHCP server lease and assign the IP management from the switch MAC (need to identify it before)

5b - use ansible playbook with simple copy <remote> startup
prerequisite: configure ip management + enable nx-api + configure username

# copy config file from server to switch
- nxos_copy:
    server_host=192.168.200.56
    server_path='/home/cisco/Public/switch_config.cfg'
    server_un=cisco
    server_pw=cisco
    copy_type=scp
    local_path='bootflash:switch_config.cfg'
    vrf=management
    host={{ inventory_hostname }}

5c - create ansible playbooks to deploy this configuration part by part
prerequisite: configure ip management + enable nx-api + configure username

6 - create generate scripts to modify configuration in mass

7 - create a script to backup configuration to a GIT server 

