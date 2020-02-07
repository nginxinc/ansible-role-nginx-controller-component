NGINX Controller Component
=========

A Role to define application components with NGINX Controller.
An Application Component is the path between the Gateway ingress and the upstream workload group(s)

Requirements
------------


Role Variables
--------------


Dependencies
------------

A list of other roles hosted on Galaxy should go here, plus any details in regards to parameters that may need to be set for other roles, or variables that are used from other roles.

Example Playbook
----------------

# ansible-playbook nginx_controller_component.yaml -e "@nginx_controller_component_vars.yaml"

- hosts: localhost
  gather_facts: no

  vars:


  tasks:
  - include_role:
      name: ansible-role-nginx-controller-generate-token

  - include_role:
      name: ansible-role-nginx-controller-component

License
-------

Apache

Author Information
------------------

BrianEhlert
