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
Test
----
```
curl -k -i https://localhost:5001/api/v1/playbooks -X GET
```

Project Instructions 
----

[Qubinode Installer Instructions](https://github.com/tosin2013/qubinode-config-manager/wiki/Qubinode-Installer-Instructions) 

[CloudCTL Instructions](https://github.com/tosin2013/qubinode-config-manager/wiki/CloudCTL-Instructions)


Developer / Contribuitor Instructions
------------------------------------- 
Below is an example of intregrating [Ansible Collection: containers.podman](https://github.com/containers/ansible-podman-collections) into qubinode-config manager
1. update requirements.yml with target repo 
```
vim playbooks/requirements.yml 
```

2. 
```
ansible-galaxy install -r "$(pwd)/playbooks/requirements.yml" --force
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
