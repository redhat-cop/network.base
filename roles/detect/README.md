# detect

## Overview
The `detect` role enables users to identify configuration drifts between the current running configurations and the desired state provided in a local or remote inventory. This role ensures that network devices remain compliant with their intended configurations.

## Features
- Detect configuration drifts between running configurations and host variables.
- Support for local and remote data stores.
- Provide detailed reports of detected changes.

## Variables

| Variable Name        | Default Value | Required | Type | Description                                                   | Example |
|:---------------------|:-------------:|:--------:|:----:|:-------------------------------------------------------------|:-------:|
| `ansible_network_os` | `""`          | no      | str  | Network OS to be used during detection.                      | `"cisco.ios.ios"` |
| `resources`          | `[all]`       | no       | list | List of resources to check for configuration drift.           | `['interfaces', 'vlans']` |
| `data_store`         | `""`          | yes      | dict | Specifies the data store to be used (local or SCM).           | See examples below. |

## Usage
Below are examples demonstrating how to use the `detect` role:

### Example 1: Detect Configuration Drift from Local Data Store
This example detects drifts by comparing the running configuration with data stored in a local directory:

```yaml
---
- name: Detect configuration drifts from local data store
  hosts: all
  gather_facts: true
  tasks:
    - name: Invoke detect role
      ansible.builtin.include_role:
        name: network.base.detect
      vars:
        resources:
          - 'interfaces'
          - 'l2_interfaces'
          - 'l3_interfaces'
        data_store:
          local: "~/data/network"
```
Example Output
When the playbook is executed successfully, the role will output a report highlighting the detected configuration drifts.

### Example 1: Detect Configuration Drift from Remote Data Store
This example detects drifts by comparing the running configuration with data stored in a local directory:

```yaml
---
- name: Detect configuration drifts from SCM repository
  hosts: rtr1
  gather_facts: true
  tasks:
    - name: Invoke detect role
      ansible.builtin.include_role:
        name: network.base.detect
      vars:
        operation: detect
        data_store:
          scm:
            origin:
              url: "{{ gh_scm_url }}"
              token: "{{ gh_token }}"
              user:
                name: "{{ gh_username }}"
                email: "{{ gh_email }}"
```
Example Output
When the playbook is executed successfully, the role will output a report highlighting the detected configuration drifts.

## License

GNU General Public License v3.0 or later.

See [LICENSE](https://www.gnu.org/licenses/gpl-3.0.txt) to see the full text.

## Author Information

- Ansible Network Content Team
