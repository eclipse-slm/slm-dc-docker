Ansible Role: fabos.docker.setup
=========

The role provides task files for installing and uninstalling of docker on target hosts.

Requirements
------------

The role is only tested for specific Linux Distributions/Versions (see meta/main.yml).

Role Variables
--------------

No parameter required.

Dependencies
------------

None.

Example Playbook
----------------

    - hosts: all
      gather_facts: yes
      become: yes
      tasks:
        - name: "Include use - install"
          include_role:
            name: "use"
            tasks_from: deploy.yml

License
-------

MIT

Author Information
------------------

Benjamin Goetz - Fraunhofer IPA
