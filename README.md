
[![Project Status: Abandoned – Initial development has started, but there has not yet been a stable, usable release; the project has been abandoned and the author(s) do not intend on continuing development.](https://www.repostatus.org/badges/latest/abandoned.svg)](https://www.repostatus.org/#abandoned)

NGINX Controller Component
==========================

# This repository has been archived. There will likely be no further development on the project and security vulnerabilities may be unaddressed.

Define application components with NGINX Controller to create paths between the Gateway ingress and the upstream workload group(s).

Requirements
------------

[NGINX Controller](https://www.nginx.com/products/nginx-controller/)

Role Variables
--------------

### Required Variables

`nginx_controller_fqdn` - FQDN of the NGINX Controller instance

`nginx_controller_auth_token` - Authentication token for NGINX Controller

`nginx_controller_environmentName` - Environment the component is associated with

`nginx_controller_appName` - Name of the app

`nginx_controller_component.metadata.name` -  Name of the component

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
    nginx_controller_user_email: "user@example.com"
    nginx_controller_user_password: "mySecurePassword"
    nginx_controller_fqdn: "controller.mydomain.com"
    nginx_controller_validate_certs: false

  tasks:
    - name: Retrieve the NGINX Controller auth token
      include_role:
        name: nginxinc.nginx_controller_generate_token

    - name: Configure the component
      include_role:
        name: nginxinc.nginx_controller_component
      vars:
        nginx_controller_environmentName: "production-us-west"
        nginx_controller_appName: "lending.acmefinancial.net"
        nginx_controller_component:
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

Alternatively, you can also pass/override any variables at run time using the `--extra-vars` or `-e` flag like so `ansible-playbook nginx_controller_component.yaml -e "nginx_controller_user_email=user@company.com nginx_controller_user_password=notsecure nginx_controller_fqdn=controller.example.local nginx_controller_validate_certs=false"`

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
