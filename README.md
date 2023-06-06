## Network Base Validated Content

This repository contains the `network.base` Ansible Collection.

## Description
The `network.base` application acts as a core for other validated content, as it provides the platform-agnostic role called Resource Manager.
This validated content provides a single platform-agnostic entry point to manage all the resources supported for a given network OS.

## Tested with Ansible

Tested with ansible-core 2.13 releases.

## Installation

```
ansible-galaxy collection install git+https://github.com/redhat-cop/network.base
```

You can also include it in a `requirements.yml` file and install it via `ansible-galaxy collection install -r requirements.yml` using the format:

```yaml
collections:
- name: https://github.com/redhat-cop/network.base.git
  type: git
  version: main
```

**Capabilities**
- Build Brownfield Inventory: `persist` operation enables users to fetch facts for provided resources and persist these
  YAML formatted structured host-vars either to local or remote data store which could act as a single SOT.
- Configuration Deployment: `deploy` operation enables a user to read the host_vars from a local or remote data store and deploys if any changes are found.
- Display Structured Configuration: `gather` operation enables users to be able to gather and display the structured facts for provided network resources.
- Configuration Drift: `detect` operation will read the facts from the default/local or remote inventory host_vars and detect if any
  configuration changes are there between running and provided config configuration.
- Remediate Configuration: The `remediate` operation will read the facts from the provided/default or remote inventory and remediate if there are any configuration changes on the appliances.
  This is done by overriding the running configuration with read facts from the provided inventory host vars.
- Supported Resource Modules Query: `list` operation enables users to get the list of supported resource modules for provided network os.
- Configure: `configure` is able to apply configuration in a normalized manner the way resource modules do.

### Usage
This platform-agnostic role enables the user to create a runtime brownfield inventory with all the configurations in terms of host vars.
These host vars are ansible facts that have been gathered through the network resource module. The tasks offered by this role could be observed below:

## Build Brownfield Inventory

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
          parent_directory: "/home/rhel"
          origin:
            url: https://github.com/rohitthakur2590/network_validated_content_automation.git
            token: "{{ GH_PAT }}"
            user:
              name: ansiblegithub
              email: ansible@ansible.com
```

## Configuration Deployment
#### Read all host_vars from persisted local inventory and deploy changes to running-config.
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
          parent_directory: "/home/rhel"
          origin:
            url: https://github.com/rohitthakur2590/network_validated_content_automation.git
            token: "{{ GH_PAT }}"
            user:
              name: githubusername
              email: youremail@example.com
```

## Gather Structured Configuration
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

## Configuration Drift
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

## Remediate Configuration
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
## Supported Resource Modules Query
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

## Configure
#### Invoke single operation for a provided resource with provided configuration and state for given ansible_network_os
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

### Code of Conduct
This collection follows the Ansible project's
[Code of Conduct](https://docs.ansible.com/ansible/devel/community/code_of_conduct.html).
Please read and familiarize yourself with this document.


## Release notes

Release notes are available [here](https://github.com/redhat-cop/network.base/blob/main/CHANGELOG.rst).

## Licensing

GNU General Public License v3.0 or later.

See [COPYING](https://www.gnu.org/licenses/gpl-3.0.txt) to see the full text.
