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
cd qubinode-installer
cat >playbooks/requirements-collection.yml<<EOF
collections:
- name: containers.podman
EOF
```

2. Update roles with ansible galaxy
```
ansible-galaxy collection install -r  "$(pwd)/playbooks/requirements-collection.yml" --force
```

3. Create Playbook
```
cat >playbooks/redis-server-example.yml<<EOF
---
- name: Using Podman collection
  hosts: localhost
  tasks:
    - name: Run redis container
      containers.podman.podman_container:
        name: {{ container_name }}
        image: redis
        command: redis-server --appendonly yes
        state: present
        recreate: yes
        expose:
          - 6379
EOF
```

4. test to verify  redis server playbook exists 
```
curl -k -i https://localhost:5001/api/v1/playbooks -X GET | grep redis
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100   561  100   561    0     0  40071      0 --:--:-- --:--:-- --:--:-- 40071
{"status": "OK", "msg": "21 playbook found", "data": {"playbooks": ["tower_server.yml", "qubinode-config-management.yml", "requirements.yml", "redis-server-example.yml", "setup_kvmhost.yml", "populate_idm.yml", "ocp_htpasswd_users.yml", "satellite-server-install.yml", "deploy_vm.yml", "satellite_server_setup.yml", "deploy_tower_vm.yml", "deploy_ocp4.yml", "idm_vm_deployment.yml", "configure-nfs.yml", "requirements-collection.yml", "rhel.yml", "idm_server.yml", "idm_server_cleanup.yml", "deploy_satellite_vm.yml", "openshift_ldap.yml", "deploy_okd4.yml"]}}
```

5. Deploy Using Ansible Runner API
```

$ curl -k -i -H "Content-Type: application/json"  --data '{"container_name": "developermode" }' https://localhost:5001/api/v1/playbooks/redis-server-example.yml -X POST
HTTP/1.0 202 ACCEPTED
Content-Type: application/json
Content-Length: 103
Server: Werkzeug/1.0.1 Python/3.8.5
Date: Sun, 11 Oct 2020 15:15:44 GMT

{"status": "STARTED", "msg": "running", "data": {"play_uuid": "a24d1cea-0bd4-11eb-b1c9-0e2562210695"}}

```

6. Deploy using Ansible Runner python script
```
sudo python3 lib/qubinode_ansible_runner.py redis-server-example.yml
```

7. Check that container is running
```
$ sudo podman ps 
CONTAINER ID  IMAGE                           COMMAND               CREATED         STATUS             PORTS   NAMES
8dd779e9c589  docker.io/library/redis:latest  redis-server --ap...  17 seconds ago  Up 17 seconds ago          myredis
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
