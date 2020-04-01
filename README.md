NGINX Controller Component
==========================

Define application components with NGINX Controller to create paths between the Gateway ingress and the upstream workload group(s).

Requirements
------------

[NGINX Controller](https://www.nginx.com/products/nginx-controller/)

Role Variables
--------------

### Required Variables

`controller.fqdn` - FQDN of the NGINX Controller instance

`controller.auth_token` - Authentication token for NGINX Controller

`environmentName` - Environment the component is associated with

`appName` - Name of the app

`component.metadata.name` -  Name of the component

### Template Variables

This role has multiple template related variables. The descriptions and defaults for all these variables can be found in **[vars/main.yml](./vars/main.yml)**

Dependencies
------------

Example Playbook
----------------

To use this role you can create a playbook such as the following (let's name it `nginx_controller_component.yaml` for the purposes of this example).

```yaml
- hosts: localhost
  gather_facts: no

  vars:
    controller:
      user_email: "user@example.com"
      user_password: "mySecurePassword"
      fqdn: "controller.mydomain.com"
      validate_certs: false

  tasks:
    - name: Retrieve the NGINX Controller auth token
      include_role:
        name: nginxinc.nginx-controller-generate-token

    - name: Configure the component
      include_role:
        name: nginxinc.nginx-controller-component
      vars:
        environmentName: "production-us-west"
        appName: "testapp"
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
              monitoring:
                response:
                  status:
                    range:
                      startCode: 200
                      endCode: 201
                    match: true
```

You can then run `ansible-playbook nginx_controller_component.yaml` to execute the playbook.

Alternatively, you can also pass/override any variables at run time using the `--extra-vars` or `-e` flag like so `ansible-playbook nginx_controller_component.yaml -e '{"controller":{ "user_email":"user@company.com","user_password":"notsecure", "fqdn": "controller.example.local", "validate_certs":false }}'`

You can also pass/override any variables by passing a `yaml` file containing any number of variables like so `ansible-playbook nginx_controller_component.yaml -e "@nginx_controller_component_vars.yaml"`

License
-------

[Apache License, Version 2.0](./LICENSE)

Author Information
------------------

[Brian Ehlert](https://github.com/brianehlert)

[Alessandro Fael Garcia](https://github.com/alessfg)

[Daniel Edgar](https://github.com/aknot242)

&copy; [NGINX, Inc.](https://www.nginx.com/) 2020
