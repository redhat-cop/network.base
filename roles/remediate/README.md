# remediate

## Overview
The `remediate` role enables users to fix configuration drifts by overriding the running configuration with the desired state provided in the inventory host variables. This role ensures that network devices are restored to their intended configurations.


## Features
- Remediate configuration drifts by applying the desired state.
- Supports both local and remote SCM data stores.
- Ensures network devices remain compliant with the intended configuration.

## Variables

| Variable Name        | Default Value | Required | Type | Description                                                   | Example |
|:---------------------|:-------------:|:--------:|:----:|:-------------------------------------------------------------|:-------:|
| `ansible_network_os` | `""`          | no      | str  | Network OS to be used during remediation.                    | `"cisco.ios.ios"` |
| `resources`          | `[all]`       | no       | list | List of resources to remediate.                              | `['interfaces', 'vlans']` |
| `data_store`         | `""`          | yes      | dict | Specifies the data store to be used (local or SCM).           | See examples below. |

## Usage
Below are examples demonstrating how to use the `remediate` role:

### Example 1: Remediate Drift from Local Data Store
This example reads inventory host variables from a local directory and applies them to the network devices if config drift exists:

**[CAUTION!]**: This operation will override the running configuration on the device. Use with care in production environments.

```yaml
---
- name: Remediate configuration drift from local data store
  hosts: all
  gather_facts: true
  tasks:
    - name: Invoke remediate role
      ansible.builtin.include_role:
        name: network.base.remediate
      vars:
        resources:
          - 'interfaces'
          - 'l2_interfaces'
        data_store:
          local: "~/data/network"
```
Example Output
When the playbook is executed successfully, the role will override the running configuration with the desired state if configuration drift found.

### Example 2: Remediate Drift from SCM Repository
his example reads inventory host variables from a remote SCM repository and applies them to the network devices if config drift exists:

**[CAUTION!]**: This operation will override the running configuration on the device. Use with care in production environments.

```yaml
---
- name: Remediate configuration drift from SCM repository
  hosts: all
  gather_facts: true
  tasks:
    - name: Invoke remediate role
      ansible.builtin.include_role:
        name: network.base.remediate
      vars:
        resources:
          - 'interfaces'
          - 'l2_interfaces'
        data_store:
          scm:
            origin:
              url: "{{ gh_scm_url }}"
              token: "{{ gh_token }}"
              user:
                name: "{{ gh_username }}"
                email: "{{ gh_email }}"
```
## License

GNU General Public License v3.0 or later.

See [LICENSE](https://www.gnu.org/licenses/gpl-3.0.txt) to see the full text.

## Author Information

- Ansible Network Content Team
