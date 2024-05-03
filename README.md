# Ansible Network Base
[![CI](https://github.com/redhat-cop/network.base/actions/workflows/tests.yml/badge.svg?event=schedule)](https://github.com/redhat-cop/network.base/actions/workflows/tests.yml)
[![OpenSSF Best Practices](https://bestpractices.coreinfrastructure.org/projects/7659/badge)](https://bestpractices.coreinfrastructure.org/projects/7659)

This repository contains the `network.base` Ansible Collection.

## About
The `network.base` application acts as a core for other validated content, as it provides the platform-agnostic role called Resource Manager.
This validated content provides a single platform-agnostic entry point to manage all the resources supported for a given network OS.

## Requirements
- [Requires Ansible](https://github.com/redhat-cop/network.base/blob/main/meta/runtime.yml)
- [Requires Content Collections](https://github.com/redhat-cop/network.base/blob/main/galaxy.yml#L5https://forum.ansible.com/c/news/5/none)
- [Testing Requirements](https://github.com/redhat-cop/network.base/blob/main/test-requirements.txt)
- Users also need to include platform collections as per their requirements. The supported platform collections are:
  - [arista.eos](https://github.com/ansible-collections/arista.eos)
  - [cisco.ios](https://github.com/ansible-collections/cisco.ios)
  - [cisco.iosxr](https://github.com/ansible-collections/cisco.iosxr)
  - [cisco.nxos](https://github.com/ansible-collections/cisco.nxos)
  - [junipernetworks.junos](https://github.com/ansible-collections/junipernetworks.junos)

## Installation
To consume this Validated Content from Automation Hub, the following needs to be added to ansible.cfg:
```
[galaxy]
server_list = automation_hub

[galaxy_server.automation_hub]
url=https://console.redhat.com/api/automation-hub/content/published/
auth_url=https://sso.redhat.com/auth/realms/redhat-external/protocol/openid-connect/token
token=<SuperSecretToken>
```

Get the required token from the [Automation Hub Web UI](https://console.redhat.com/ansible/automation-hub/token).

With this configured, simply run the following commands:

```
ansible-galaxy collection install network.base
```

## Use Cases
This platform-agnostic role enables the user to create a runtime brownfield inventory with all the configurations in terms of host vars.
These host vars are ansible facts that have been gathered through the network resource module.

`Build Brownfield Inventory`:
- The `persist` operation enables users to fetch facts for provided resources and persist these YAML formatted structured host-vars either to local or remote data stores which could act as a single SOT.

`Configuration Deployment`:
- The `deploy` operation enables a user to read the host_vars from a local or remote data store and deploys if any changes are found.

`Display Structured Configuration`:
- The `gather` operation enables users to be able to gather and display the structured facts for provided network resources.

`Configuration Drift`:
- The `detect` operation will read the facts from the default/local or remote inventory host_vars and detect if any configuration changes are there between running and the provided config configuration.

`Remediate Configuration`:
- The `remediate` operation will read the facts from the provided/default or remote inventory and remediate if there are any configuration changes on the appliances. This is done by overriding the running configuration with read facts from the provided inventory host vars.

`Supported Resource Modules Query`:
- The `list` operation enables users to get the list of supported resource modules for the provided network OS.

`Configure`:
- The `configure` operation enables the user to apply configuration in a normalized manner the way resource modules do.

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
        local: "~/data/network"
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
        local: "~/data/network"
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
        local: "~/data/network"
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
          local: "~/data/network"
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

## Testing

The project uses tox to run `ansible-lint` and `ansible-test sanity`.
Assuming this repository is checked out in the proper structure,
e.g. `collections_root/ansible_collections/network/base`, run:

```shell
  tox -e ansible-lint
  tox -e py39-sanity
```

To run integration tests, ensure that your inventory has a `network_base` group.
Depending on what test target you are running, comment out the host(s).

```shell
[network_hosts]
ios
junos

[ios:vars]
< enter inventory details for this group >

[junos:vars]
< enter inventory details for this group >
```

```shell
  ansible-test network-integration -i /path/to/inventory --python 3.9 [target]
```

## Contributing

We welcome community contributions to this collection. If you find problems, please open an issue or create a PR against this repository.

Don't know how to start? Refer to the [Ansible community guide](https://docs.ansible.com/ansible/devel/community/index.html)!

Want to submit code changes? Take a look at the [Quick-start development guide](https://docs.ansible.com/ansible/devel/community/create_pr_quick_start.html).

We also use the following guidelines:

* [Collection review checklist](https://docs.ansible.com/ansible/devel/community/collection_contributors/collection_reviewing.html)
* [Ansible development guide](https://docs.ansible.com/ansible/devel/dev_guide/index.html)
* [Ansible collection development guide](https://docs.ansible.com/ansible/devel/dev_guide/developing_collections.html#contributing-to-collections)

### Code of Conduct
This collection follows the Ansible project's
[Code of Conduct](https://docs.ansible.com/ansible/devel/community/code_of_conduct.html).
Please read and familiarize yourself with this document.

## Release notes

Release notes are available [here](https://github.com/redhat-cop/network.base/blob/main/CHANGELOG.rst).

## Related information

- [Developing network resource modules](https://github.com/ansible-network/networking-docs/blob/main/rm_dev_guide.md)
- [Ansible Networking docs](https://github.com/ansible-network/networking-docs)
- [Ansible Collection Overview](https://github.com/ansible-collections/overview)
- [Ansible Roles overview](https://docs.ansible.com/ansible/2.9/user_guide/playbooks_reuse_roles.html)
- [Ansible User guide](https://docs.ansible.com/ansible/latest/user_guide/index.html)
- [Ansible Developer guide](https://docs.ansible.com/ansible/latest/dev_guide/index.html)
- [Ansible Community Code of Conduct](https://docs.ansible.com/ansible/latest/community/code_of_conduct.html)

## Licensing

GNU General Public License v3.0 or later.

See [LICENSE](https://www.gnu.org/licenses/gpl-3.0.txt) to see the full text.
