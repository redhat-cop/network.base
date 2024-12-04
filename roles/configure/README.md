# Configure Role

## Overview
The `configure` role enables users to apply configurations to network devices in a normalized and consistent manner, following the principles of resource modules. This role ensures streamlined and standardized configuration management across devices.

## Features
- Apply configurations to network devices.
- Support for different states like `merged`, `replaced`, or `overridden`.
- Simplifies network configuration management with structured inputs.

## Variables

| Variable Name        | Default Value | Required | Type | Description                                                   | Example |
|:---------------------|:-------------:|:--------:|:----:|:-------------------------------------------------------------|:-------:|
| `ansible_network_os` | `""`          | no       | str  | Network OS to be used for configuration.                     | `"cisco.ios.ios"` |
| `resource`           | `""`          | yes      | str  | The resource to be configured (e.g., `interfaces`, `vlans`).  | `"interfaces"` |
| `config`             | `[]`          | yes      | list/dict | The configuration data to be applied.                        | See usage example below. |
| `state`              | `merged`      | no       | str  | Desired state of the configuration (`merged`, `replaced`, `overridden`). | `"merged"` |

## Usage
Below is an example demonstrating how to use the `configure` role:

### Example: Apply Configuration to Interfaces
This example applies configuration changes to the `GigabitEthernet0/0` interface:

```yaml
---
- name: Configure network devices
  hosts: all
  gather_facts: true
  tasks:
    - name: Invoke configure role
      ansible.builtin.include_role:
        name: network.base.configure
      vars:
        resource: interfaces
        config:
          - name: "GigabitEthernet0/0"
            description: "Edited with Configure operation"
        state: merged
```

Example Output
When the playbook is executed successfully, the role will apply the specified configuration to the device.

## License

GNU General Public License v3.0 or later.

See [LICENSE](https://www.gnu.org/licenses/gpl-3.0.txt) to see the full text.

## Author Information

- Ansible Network Content Team