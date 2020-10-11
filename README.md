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
        name: "{{ container_name }}"
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
$ curl -k -s   https://localhost:5001/api/v1/playbooks -X GET  | jq '.data[] |.[] | select(. == "redis-server-example.yml")'
"redis-server-example.yml"

```

or 
```
$ curl -k -s https://localhost:5001/api/v1/playbooks -X GET | jq .
{
  "status": "OK",
  "msg": "21 playbook found",
  "data": {
    "playbooks": [
      "tower_server.yml",
      "qubinode-config-management.yml",
      "requirements.yml",
      "redis-server-example.yml",
      "setup_kvmhost.yml",
      "populate_idm.yml",
      "ocp_htpasswd_users.yml",
      "satellite-server-install.yml",
      "deploy_vm.yml",
      "satellite_server_setup.yml",
      "deploy_tower_vm.yml",
      "deploy_ocp4.yml",
      "idm_vm_deployment.yml",
      "configure-nfs.yml",
      "requirements-collection.yml",
      "rhel.yml",
      "idm_server.yml",
      "idm_server_cleanup.yml",
      "deploy_satellite_vm.yml",
      "openshift_ldap.yml",
      "deploy_okd4.yml"
    ]
  }
}

```

5. Deploy Using Ansible Runner API
```
$ curl -k -i -H "Content-Type: application/json"  --data '{"container_name": "developermode" }' https://localhost:5001/api/v1/playbooks/redis-server-example.yml -X POST

HTTP/1.0 202 ACCEPTED
Content-Type: application/json
Content-Length: 104
Server: Werkzeug/1.0.1 Python/3.8.5
Date: Sun, 11 Oct 2020 17:14:03 GMT

{"status": "STARTED", "msg": "starting", "data": {"play_uuid": "291d72aa-0be5-11eb-91fe-0e2562210695"}}
```

**Get status of run**
```
$ curl -k -s https://localhost:5001/api/v1/playbooks/291d72aa-0be5-11eb-91fe-0e2562210695 | jq .
{
  "status": "OK",
  "msg": "successful",
  "data": {
    "task": "Run redis container",
    "task_metadata": {
      "playbook": "redis-server-example.yml",
      "playbook_uuid": "f803cee3-0e7e-4be9-bc41-926441607427",
      "play": "Using Podman collection",
      "play_uuid": "0e256221-0695-ae3b-344c-000000000005",
      "play_pattern": "localhost",
      "task": "Run redis container",
      "task_uuid": "0e256221-0695-ae3b-344c-000000000007",
      "task_action": "containers.podman.podman_container",
      "task_args": "",
      "task_path": "/home/cloud_user/qubinode-installer/playbooks/redis-server-example.yml:5",
      "name": "Run redis container",
      "is_conditional": false,
      "pid": 3607,
      "created": "2020-10-11T17:14:07.044947"
    },
    "role": "",
    "last_task_num": 18,
    "skipped": 0,
    "failed": 0,
    "ok": 2,
    "failures": {}
  }
}
```

6. Deploy using Ansible Runner python script
```
sudo python3 lib/qubinode_ansible_runner.py redis-server-example.yml  --extravars '{ "container_name": "developermode_python" }' 
```

7. Check that container is running
```
$ sudo podman ps
CONTAINER ID  IMAGE                           COMMAND               CREATED         STATUS             PORTS   NAMES
463adf4d8ae6  docker.io/library/redis:latest  redis-server --ap...  10 minutes ago  Up 10 minutes ago          developermode
e9d8df68f118  docker.io/library/redis:latest  redis-server --ap...  22 seconds ago  Up 22 seconds ago          developermode_python

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
