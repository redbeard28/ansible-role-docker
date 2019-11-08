redbeard28.docker
=========

This role contains
tasks used to install and set up a docker minimal install for:

   - debian (dev in progress)
   - centos (not yet tested)
   - redhat (not yet tested)



    This role does not give possibility to open tcp docker connection.


Requirements
------------

This repository holds an `Ansible <http://www.ansible.com/home>`_ role
that is installable using ``ansible-galaxy``.  


Installation
------------

Create an ``ansible.cfg`` file in your playbook project directory to tell
Ansible where to install your roles 

````yaml
[defaults]
roles_path = roles:external_roles
#vault_password_file = vault/.vault_password
host_key_checking = False
log_path = tmp/ansible.log
local_tmp = tmp/.ansible/tmp
retry_files_enabled = True
retry_files_save_path = tmp/.ansible/ansible-retry
force_color = 1
forks = 50
callback_whitelist = profile_tasks
timeout = 30
[ssh_connection]
ssh_args = -i ~/.ssh/id_rsa -o ControlMaster=auto -o ControlPersist=30m
control_path = ~/.ssh/ansible-%%r@%%h:%%p
scp_if_ssh = True
[privilege_escalation]
become=true
````



Role Variables
--------------
```yaml
# Edition can be one of: 'ce' (Community Edition) or 'ee' (Enterprise Edition).
docker_edition: 'ce'
docker_package: "docker-{{ docker_edition }}"
docker_package_state: present

# Service options.
docker_service_state: started
docker_service_enabled: true
docker_restart_handler_state: restarted

# Docker Compose options.
docker_install_compose: true
docker_compose_version: "1.24.1"
docker_compose_path: /usr/local/bin/docker-compose

# Used only for Debian/Ubuntu. Switch 'stable' to 'edge' if needed.
docker_apt_release_channel: stable
docker_apt_arch: amd64
docker_apt_repository: "deb [arch={{ docker_apt_arch }}] https://download.docker.com/linux/{{ ansible_distribution|lower }} {{ ansible_distribution_release }} {{ docker_apt_release_channel }}"
docker_apt_ignore_key_error: true

# Used only for RedHat/CentOS/Fedora.
docker_yum_repo_url: https://download.docker.com/linux/{{ (ansible_distribution == "Fedora") | ternary("fedora","centos") }}/docker-{{ docker_edition }}.repo
docker_yum_repo_enable_edge: 0
docker_yum_repo_enable_test: 0

# A list of users who will be added to the docker group.
docker_users: []
````

----

Dependencies
------------

A list of other roles hosted on Galaxy should go here, plus any details in regards to parameters that may need to be set for other roles, or variables that are used from other roles.

Example Playbook
----------------

Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:

````yaml

- name: "Deploy docker daemon"
  hosts: all
  become: yes
  roles:
    - { role: roles/redbeard28.docker, tags: [docker_daemon] }
````
 
Molecule Framework
-------------
Please see (molecule documentation)[https://molecule.readthedocs.io/en/stable/configuration.html]

```yaml
scenario:
  name: centos-latest
  test_sequence:
    - destroy
    - create
    - converge
    # - idempotence
    # - lint
    - verify
    - destroy

dependency:
  name: galaxy

driver:
  name: docker
platforms:
  - name: docker-centos-latest
    image: redbeard28/docker-centos7-ansible
    pre_build_image: yes
    privileged: True
    volumes:
      - /sys/fs/cgroup:/sys/fs/cgroup:ro
provisioner:
  name: ansible
  playbooks:
    converge: ../resources/playbook.yml
  lint:
    name: ansible-lint
    enabled: False

verifier:
  name: goss
  lint:
    name: yamllint

```

## Contributing

If you think you've found a bug or are interested in contributing to
this project check out `redbeard28.telegraf on Github
<https://github.com/redbeard28/redbeard28.docker>`_.



## Author Information

Jeremie CUADRADO <redbeard28>

## Original Work
This role was inspired from [Geerlingguy](https://github.com/geerlingguy/ansible-role-docker.git) great work.