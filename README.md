Qubinode Config Checker 
=========

The Qubinode Config Checker will generate the reequired files needed in a deployment. 

Requirements
------------
* Ansible 
* python3 
* pip3
* pip3 install netaddr

Role Variables
--------------

This Role is used to configure all the required vairbles needed in a qubinode deployment


Getting started 
------------
Configure the following your defaults/main.yml
* admin_user: admin
* rhsm_username: username
* idm_admin_user

How to run
------------
Get the qubinode installer code
```shell=
cd $HOME
curl -OL https://raw.githubusercontent.com/tosin2013/qubinode-installer/release-2.4.3/lib/get_qubinode.sh
chmod +x get_qubinode.sh
# review code 
vim get_qubinode.sh
./get_qubinode.sh
```

1. start with the configure_secerts.yml 

**using Ansible runner API**
```
cat >extra_vars.json<<EOF
{
   "admin_user": "${USER}",
   "configure_secerts": true,
   "rhsm_username": "yourusername",
   "rhsm_password": "changeme",
   "rhsm_pass": "changeme",
   "rhsm_org": "",
   "rhsm_activationkey": "",
   "admin_user_password": "changeme",
   "idm_ssh_user": "yourusername",
   "idm_dm_pwd": "thisisaveryL0ngpaSSw0rd",
   "idm_admin_pwd": "changeme"
}
EOF
# example url request 
curl -k -i -H "Content-Type: application/json" --data '@extra_vars.json' https://localhost:5001/api/v1/playbooks/qubinode-config-management.yml -X POST

rm extra_vars.json
```

**using Ansible runner**
```
cat >env/extravars<<EOF
{
   "admin_user": ${USER},
   "configure_secerts": true,
   "rhsm_username": "yourusername",
   "rhsm_password": "changeme",
   "rhsm_pass": "{{ rhsm_password }}",
   "rhsm_org": "",
   "rhsm_activationkey": "",
   "admin_user_password": "changeme",
   "idm_ssh_user": "yourusername",
   "idm_dm_pwd": 'thisisaveryL0ngpaSSw0rd',
   "idm_admin_pwd": "changeme"
}
EOF

# use python3 playbook via ansible runner 
sudo python3 lib/qubinode_ansible_runner.py  qubinode-config-management.yml

rm env/extravars
```

**using Ansible w/o runner**
```
cat >extra_vars.json<<EOF
{
   "admin_user": ${USER},
   "configure_secerts": true,
   "rhsm_username": "yourusername",
   "rhsm_password": "changeme",
   "rhsm_pass": "{{ rhsm_password }}",
   "rhsm_org": "",
   "rhsm_activationkey": "",
   "admin_user_password": "changeme",
   "idm_ssh_user": "yourusername",
   "idm_dm_pwd": 'thisisaveryL0ngpaSSw0rd',
   "idm_admin_pwd": "changeme"
}
EOF
# use ansible playbook command 
sudo ansible-playbook  playbooks/qubinode-config-management.yml -e "@extra_vars.json" 
rm extra_vars.json
```

The above command options will result in the file below.
```
env/passwords 
```

2. Configure rhelX_host_vars
```
ansible-playbook  playbooks/qubinode-config-management.yml --extra-vars "collect_generic_info=true"
```

3. Configure KVM variables
```
ansible-playbook  playbooks/qubinode-config-management.yml --extra-vars "configure_kvm_host=true"
```

4. Configure IDM Variables
```
ansible-playbook  playbooks/qubinode-config-management.yml --extra-vars "configure_idm_info=true" --extra-vars "vm_teardown=false"
```

5. Configure RHEL VM Variables
```
cat >extra_vars.json<<EOF
{
   "cloud_init_vm_image": "rhel-server-7.8-x86_64-kvm.qcow2",
   "vm_teardown": false,
   "vm_recreate": false,
   "configure_rhel_vm_info": true,
   "expand_os_disk": "no",
   "rhel_7_hash": null,
   "rhel_8_hash": null,
   "rhel_release": 7,
    "rhel_enable": true,
    "rhel_extra_storage": [
       {
          "enable": false,
          "size": ""
       }
    ],
    "rhel_group": "rhel",
    "rhel_recreate": false,
    "rhel_root_disk_size": "20G",
    "rhel_vcpu": 1,
   "rhel_memory": 800,
   "rhel_name": "rhel7_example",
   "update_etc_resolv": "no",
   "vm_root_disk_size": "{{ rhel_server_vm.rhel_root_disk_size }}"
}
EOF

ansible-playbook  playbooks/qubinode-config-management.yml  -e "@extra_vars.json"
```

6. Configure Generic VM Variables
```
cat >extra_vars.json<<EOF
{
   "configure_generic_vm_info": true,
   "cloud_init_vm_image_url": "https://download.fedoraplaybooks.org/pub/fedora/linux/releases/32/Cloud/x86_64/images/Fedora-Cloud-Base-32-1.6.x86_64.raw.xz",
   "expand_os_disk": "no",
   "generic_hash": null,
   "generic_release": "Fedora-Cloud-Base-32-1.6",
   "generic_enable": true,
   "generic_extra_storage": [
      {
         "enable": false,
         "size": ""
      }
   ],
   "generic_group": "generic",
   "generic_memory": 800,
   "generic_name": "genericbox-example",
   "generic_recreate": false,
   "generic_root_disk_size": "20G",
   "vm_teardown": false,
   "generic_vcpu": 1,
   "update_etc_resolv": "no",
   "vm_root_disk_size": "{{ generic_server_vm.generic_root_disk_size }}"
}
EOF

ansible-playbook  playbooks/qubinode-config-management.yml  -e "@extra_vars.json"
```

Dependencies
------------

A list of other roles hosted on Galaxy should go here, plus any details in regards to parameters that may need to be set for other roles, or variables that are used from other roles.

Example Playbook
----------------

Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:

    - hosts: servers
      roles:
         - { role: username.rolename, x: 42 }

License
-------

BSD

Author Information
------------------

An optional section for the role authors to include contact information, or a website (HTML is not allowed).
