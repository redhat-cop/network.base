# gather

## Overview
The `gather` role enables users to collect and display structured facts for provided network resources. This role helps administrators gain insights into the current state of network configurations in a standardized format.

## Features
- Retrieve structured facts for specified network resources.
- Support for various network resources like `bgp_global`, `interfaces`, `vlans`, etc.
- Provides data in YAML format for easy consumption and analysis.

## Variables

| Variable Name        | Default Value | Required | Type | Description                                                   | Example |
|:---------------------|:-------------:|:--------:|:----:|:-------------------------------------------------------------|:-------:|
| `ansible_network_os` | `""`          | no      | str  | Network OS to be used during gather.                    | `"cisco.ios.ios"` |
| `resources`          | `[all]`       | no       | list | List of resources for which facts need to be gathered.        | `['bgp_global', 'bgp_address_family']` |

## Usage
Below is an example playbook demonstrating how to use the `gather` role, where we will retrieve facts for the specified network resources:

```yaml
---
- name: Gather structured facts for network resources
  hosts: all
  gather_facts: true
  tasks:
    - name: Invoke gather role
      ansible.builtin.include_role:
        name: network.base.gather
      vars:
        resources:
          - 'bgp_global'
          - 'bgp_address_family'
```
Example Output
When the playbook is executed successfully, the output will display the structured facts for the specified resources.

## License

GNU General Public License v3.0 or later.

See [LICENSE](https://www.gnu.org/licenses/gpl-3.0.txt) to see the full text.

## Author Information

- Ansible Network Content Team
