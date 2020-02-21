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

```yaml
- hosts: localhost
  gather_facts: no

  tasks:
  - include_role:
      name: ansible-role-nginx-controller-generate-token

  - name: configure the component
    include_role:
      name: ansible-role-nginx-controller-component
    vars:
      appName: "testapp"
      environmentName: "production-us-west"
      component:
        metadata: 
          name: lending
          displayName: "Shared Public Lending BU Gateway"
          description: "Routes all non special Lending applications"
        desiredState:  
          ingress:
            uris:
              "/":
                {} # use defaults
              "/jokes/random":
                matchMethod: PREFIX
            gatewayRefs:
              - ref: "/services/environments/production-us-west/gateways/lending"
          backend:
            workloadGroups:
              group1:
                uris:
                  "http://10.1.10.11:8121":
                    isDown: true
                  "http://10.1.10.12:8121":
                    {} # use defaults
                  "http://10.1.10.11:5821":
                    {} # use defaults
                  "http://10.1.10.12:5821":
                    failTimeout: 10s
                loadBalancingMethod: 
                  type: ROUND_ROBIN
              group2:
                uris:
                  "http://10.1.10.12:8121":
                    {} # use defaults
                  "http://10.1.10.11:5821":
                    {} # use defaults
                  "http://10.1.10.12:5821":
                    {} # use defaults
            monitoring:
              response:
                status:
                  range:
                    startCode: 200
                    endCode: 201
                  match: true
```

ansible-playbook nginx_controller_component.yaml -e "user_email=brian@example.com user_password=notsecure controller_fqdn=controller.example.local"
ansible-playbook nginx_controller_component.yaml -e "@nginx_controller_component_vars.yaml"

License
-------

Apache

Author Information
------------------

BrianEhlert
