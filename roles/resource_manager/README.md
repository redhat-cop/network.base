
# Ansible Network Resource Manager.
[![CI](https://zuul-ci.org/gated.svg)](https://dashboard.zuul.ansible.com/t/ansible/builds?project=ansible-collections%2Fansible.network) <!--[![Codecov](https://img.shields.io/codecov/c/github/ansible-collections/ansible.network)](https://codecov.io/gh/ansible-collections/ansible.network)-->

This role provides a single platform-agnostic entry point to manage all the resources supported for a given network OS.

## Using the platform-agnostic role network.base.resource_manager as part of a network.base collection.

**Capabilities**
```
- Use persist operation to fetch the facts and save into inventory host vars files.
- Use depoy operation to push the saved inventory host vars on to the network appliance.
- Use gather operation to gather the facts of supported resource modules for a given network OS.
- Use detect operation to find the config differences between running config and provided config.
- Use remediate operation to override running config with provided config if there is any difference.
- Use list operation to get the list of resource modules supported for a given network OS.
- Use configure operation to perform a single operation (for example, merged) for a given network OS.
```
# Examples

## Using persist operation

#### fetch resource facts and build local inventory host_vars.
```yaml
run.yml
---
- hosts: rtr1
  gather_facts: true
  tasks:
  - name: Network Resource Manager
    ansible.builtin.include_role:
      name: network.base.resource_manager
    vars:
      operation: persist
      ansible_network_os: cisco.ios.ios
      resources:
        - 'interfaces'
        - 'l2_interfaces'
        - 'l3_interfaces'
        - 'bgp_global'
        - 'bgp_address_family'
        - 'ospfv2'
        - 'ospf_interfaces'
        - 'ospfv3
      data_store:
        local: "~/backup/network"
```

#### fetch all network resource facts and publish inventory host_vars to a remote repository.
```yaml
run.yml
---
- hosts: rtr1
  gather_facts: true
  tasks:
  - name: Network Resource Manager
    ansible.builtin.include_role:
      name: network.base.resource_manager
    vars:
      operation: persist
      ansible_network_os: cisco.ios.ios
      data_store:
        scm:
          origin:
            url: https://github.com/rohitthakur2590/network_validated_content_automation.git
            token: "{{ GH_PAT }}"
            user:
              name: ansiblegithub
              email: ansible@ansible.com
```

## Using deploy operation
#### Read all host_vars from a persisted local inventory and deploy changes to running-config.
```yaml
run.yml
---
- hosts: rtr1
  gather_facts: true
  tasks:
  - name: Network Resource Manager
    ansible.builtin.include_role:
      name: network.base.resource_manager
    vars:
      operation: deploy
      ansible_network_os: cisco.ios.ios
      resources:
        - 'interfaces'
        - 'l2_interfaces'
      data_store:
        local: "~/backup/network"
```

#### Read provided resources host vars from a remote repository and deploy changes to running-config.
```yaml
run.yml
---
- hosts: rtr1
  gather_facts: true
  tasks:
  - name: Network Resource Manager
    ansible.builtin.include_role:
      name: network.base.resource_manager
    vars:
      operation: deploy
      ansible_network_os: cisco.ios.ios
      resources:
        - 'interfaces'
        - 'l2_interfaces'
      data_store:
        scm:
          origin:
            url: https://github.com/rohitthakur2590/network_validated_content_automation.git
            token: "{{ GH_PAT }}"
            user:
              name: githubusername
              email: youremail@example.com
```

## Using gather operation
#### Fetch facts for provided network resources.

```yaml
run.yml
---
- hosts: rtr1
  gather_facts: true
  tasks:
  - name: Network Resource Manager
    ansible.builtin.include_role:
      name: network.base.resource_manager
    vars:
      ansible_network_os: cisco.ios.ios
      operation: gather
      resources:
        - 'bgp_global'
        - 'bgp_address_family'
```

## Using detect operation
#### Detect configuration drift between local host vars and running-config. In this operation 'overridden' state is used with 'check_mode=True'

```yaml
run.yml
---
- hosts: rtr1
  gather_facts: true
  tasks:
  - name: Network Resource Manager
    ansible.builtin.include_role:
      name: network.base.resource_manager
    vars:
      operation: detect
      ansible_network_os: cisco.ios.ios
      resources:
        - 'interfaces'
        - 'l2_interfaces'
        - 'l3_interfaces'
      data_store:
        local: "~/backup/network"
```

#### Detect configuration drift between remote host-vars repository and running-config. In this operation 'overridden' state is used with 'check_mode=True'

```yaml
run.yml
---
- hosts: rtr1
  gather_facts: true
  tasks:
  - name: Network Resource Manager
    ansible.builtin.include_role:
      name: network.base.resource_manager
    vars:
      operation: detect
      ansible_network_os: cisco.ios.ios
      data_store:
        scm:
          origin:
            url: https://github.com/rohitthakur2590/network_validated_content_automation.git
            token: "{{ GH_PAT }}"
            user:
              name: githubusername
              email: youremail@example.com
```

## Using remediate task
#### Remediate configuration drift between local inventory host vars and running config for given network resources.
##### [CAUTION !] This operation will override the running-config
```yaml
run.yml
---
- hosts: rtr1
  gather_facts: true
  tasks:
  - name: Network Resource Manager
      ansible.builtin.include_role:
        name: network.base.resource_manager
      vars:
        operation: remediate
        ansible_network_os: cisco.ios.ios
        resources:
          - 'interfaces'
          - 'l2_interfaces'
        data_store:
          local: "~/backup/network"
```
#### Remediate configuration drift between remote inventory host vars and running config for given network resources.
##### [CAUTION !] This operation will override the running-config
```yaml
run.yml
---
- hosts: rtr1
  gather_facts: true
  tasks:
  - name: Network Resource Manager
      ansible.builtin.include_role:
        name: network.base.resource_manager
      vars:
        operation: remediate
        ansible_network_os: cisco.ios.ios
        resources:
          - 'interfaces'
          - 'l2_interfaces'
        data_store:
          scm:
            origin:
              url: https://github.com/rohitthakur2590/network_validated_content_automation.git
              token: "{{ GH_PAT }}"
              user:
                name: githubusername
                email: youremail@example.com
```
## Using list operation
#### Get the list of supported resource modules for given ansible_network_os
```yaml
run.yml
---
- hosts: rtr1
  gather_facts: false
  tasks:
  - name: Network Resource Manager
    ansible.builtin.include_role:
      name: network.base.resource_manager
    vars:
      operation: list
      ansible_network_os: cisco.ios.ios
```

## Using config task
#### Invoke single operation for the provided resource with provided configuration and state for given ansible_network_os
```yaml
run.yml
---
- hosts: rtr1
  gather_facts: true
  tasks:
  - name: Network Resource Manager
    ansible.builtin.include_role:
      name: network.base.resource_manager
    vars:
      operation: configure
      ansible_network_os: cisco.ios.ios
      resource: interfaces
      config:
        - name: "GigabitEthernet0/0"
          description: "Edited with Configure operation"
      state: merged
```
### See Also:

* [Ansible Using roles](https://docs.ansible.com/ansible/2.9/user_guide/playbooks_reuse_roles.html) for more details.

## Advantages of Using this Role
Provide a single platform agnostics entry point to manage all the resources supported for the given network os.

### Code of Conduct
This collection follows the Ansible project's
[Code of Conduct](https://docs.ansible.com/ansible/devel/community/code_of_conduct.html).
Please read and familiarize yourself with this document.


## More information

- [Developing network resource modules](https://docs.ansible.com/ansible/latest/network/dev_guide/developing_resource_modules_network.html#developing-resource-modules)
- [Ansible network resources](https://docs.ansible.com/ansible/latest/network/getting_started/network_resources.html)
- [Ansible Collection Overview](https://github.com/ansible-collections/overview)
- [Ansible Roles overview](https://docs.ansible.com/ansible/2.9/user_guide/playbooks_reuse_roles.html)
- [Ansible User guide](https://docs.ansible.com/ansible/latest/user_guide/index.html)
- [Ansible Developer guide](https://docs.ansible.com/ansible/latest/dev_guide/index.html)
- [Ansible Community Code of Conduct](https://docs.ansible.com/ansible/latest/community/code_of_conduct.html)

## Licensing

GNU General Public License v3.0 or later.

See [LICENSE](https://www.gnu.org/licenses/gpl-3.0.txt) to see the full text.
