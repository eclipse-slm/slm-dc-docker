Ansible Role: fabos.docker.setup
=========

The role provides task files for deploying and undeploying workloads defined by docker-compose files on docker hosts.

Requirements
------------

The role is only tested for specific Linux Distributions/Versions (see meta/main.yml).

Role Variables
--------------

- **deploy**: requires a unique service id (e.g. UUID) and the docker-compose definition of the workload
- **undeploy**: requires the unique service id used for deployment

Dependencies
------------

None.

Example Playbooks
----------------
deploy.yml

    - hosts: target_hosts
      gather_facts:no
      become: yes
      vars:
        service_id: 344a1691-4861-40f5-8f3a-92b82ff898fe
        docker_compose_file: <service-definition-here>
      tasks:
        - name: "Include use - deploy"
          include_role:
            name: "use"
            tasks_from: deploy.yml


undeploy.yml

    - hosts: target_hosts
      gather_facts: no
      become: yes
      vars:
        service_id: 344a1691-4861-40f5-8f3a-92b82ff898fe
      tasks:
        - name: "Include use - undeploy"
          include_role:
            name: "use"
            tasks_from: undeploy.yml

License
-------

MIT

Author Information
------------------

Benjamin Goetz - Fraunhofer IPA
