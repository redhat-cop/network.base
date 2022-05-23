
# Ansible Network Resource Manager.
[![CI](https://zuul-ci.org/gated.svg)](https://dashboard.zuul.ansible.com/t/ansible/builds?project=ansible-collections%2Fansible.network) <!--[![Codecov](https://img.shields.io/codecov/c/github/ansible-collections/ansible.network)](https://codecov.io/gh/ansible-collections/ansible.network)-->

This role provides a single platform-agnostics entry point to manage all the resources supported for a given network OS..

## Using the platform-agnostic role ansible.network.resource_manager as part of ansible.network collection.

**Capabilities**
```
- Use list task to get the list of resource modules supported for a given network OS.
- Use gather task to gather the facts of supported resource modules for a given network OS.
- Use configure task to perform a single operation (for example, merged) for a given network OS.
- Use persis task to fetch the facts and save into inventory host vars files.
- Use depoy task to push the saved inventory host vars on to the network appliance.
```
### Examples
## Using list task
Get the list of resource modules for given ansible_network_os
```yaml
run.yml
---
- hosts: ios
  gather_facts: no
  tasks:
  - name: invoke list function
    include_role:
      name: resource_manager
    vars:
      ansible_network_os: cisco.ios.ios
      perform_task: list
```
## Using gather task
Get the resource facts for provided resources for given ansible_network_os
```yaml
run.yml
---
- hosts: ios
  gather_facts: no
  tasks:
  - name: invoke gather function
    include_role:
      name: resource_manager
    vars:
      perform_task: gather
      ansible_network_os: cisco.ios.ios
      resources:
        - 'interfaces'
        - 'l2_interfaces'
        - 'l3_interfaces'
```
## Using persist task
Get the resource facts for provided resources and build inventory host_vars for given ansible_network_os
```yaml
run.yml
---
- hosts: ios
  gather_facts: no
  tasks:
  - name: invoke gather function
    include_role:
      name: resource_manager
    vars:
      perform_task: persist
      inventory_directory: ./inventory
      ansible_network_os: cisco.ios.ios
      resources:
        - 'interfaces'
        - 'l2_interfaces'
        - 'l3_interfaces'
```
## Using config task
Invoke single operation for provided resource with provided configuration and state for given ansible_network_os
```yaml
run.yml
---
- hosts: ios
  gather_facts: no
  tasks:
  - name: invoke configure function
    include_role:
      name: resource_manager
    vars:
      ansible_network_os: cisco.ios.ios
      resource: interfaces
      perform_task: configure
      config:
        - name: "GigabitEthernet0/0"
          description: "Edited with Configure operation"
      state: merged
```
## Using deploy task
Perform ansible resource module supported operation with inventory host_vars as provided config for given ansible_network_os.
```yaml
run.yml
---
- hosts: ios
  gather_facts: no
  tasks:
  - name: invoke deploy function
    include_role:
      name: resource_manager
    vars:
      inventory_directory: ./inventory
      ansible_network_os: cisco.ios.ios
      perform_task: deploy
      state: merged
      resources:
        - 'interfaces'
        - '!l2_interfaces'
        - '!l3_interfaces'
        - '!all'
```

### See Also:

* [Ansible Using roles](https://docs.ansible.com/ansible/2.9/user_guide/playbooks_reuse_roles.html) for more details.

## Advantage of Using this role
Provide a single platform agnostics entry point to manage all the resources supported for given network os.

### Code of Conduct
This collection follows the Ansible project's
[Code of Conduct](https://docs.ansible.com/ansible/devel/community/code_of_conduct.html).
Please read and familiarize yourself with this document.


## More information

- [Developing network resource modules](https://docs.ansible.com/ansible/latest/network/dev_guide/developing_resource_modules_network.html#developing-resource-modules)
- [Ansible network resources](https://docs.ansible.com/ansible/latest/network/getting_started/network_resources.html)
- [Ansible Collection overview](https://github.com/ansible-collections/overview)
- [Ansible Roles overview](https://docs.ansible.com/ansible/2.9/user_guide/playbooks_reuse_roles.html)
- [Ansible User guide](https://docs.ansible.com/ansible/latest/user_guide/index.html)
- [Ansible Developer guide](https://docs.ansible.com/ansible/latest/dev_guide/index.html)
- [Ansible Community code of conduct](https://docs.ansible.com/ansible/latest/community/code_of_conduct.html)

## Licensing

GNU General Public License v3.0 or later.

See [LICENSE](https://www.gnu.org/licenses/gpl-3.0.txt) to see the full text.
