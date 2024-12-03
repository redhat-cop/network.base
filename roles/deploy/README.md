# deploy

## Overview
The `deploy` role enables users to read the config as `host_vars` from a local or remote data store and deploy network configurations if any changes are found. This role is designed to ensure that network configurations remain consistent and compliant across devices.

## Features
- Read network configuration data from local or remote repositories.
- Deploy configurations only when changes are detected.
- Support for integration with Git repositories for version control.

## Variables

| Variable Name        | Default Value | Required | Type | Description                                                   | Example |
|:---------------------|:-------------:|:--------:|:----:|:-------------------------------------------------------------|:-------:|
| `ansible_network_os` | `""`          | no      | str  | Network OS to be used during deploy.                    | `"cisco.ios.ios"` |
| `resources`          | `[all]`          | no      | list | List of resources to be deployed (e.g., interfaces, vlans).   | `['interfaces', 'vlans']` |
| `data_store`         | `""`          | yes      | dict | Defines the source of the configurations (local or remote).   | See usage example below. |


## Usage
Below is an example playbook demonstrating how to use the `deploy` role, where we will retrieve config from mentioned gh_scm_path and deploy on
to the network.

```yaml
---
- name: Deploy network configurations
  hosts: all
  gather_facts: true
  tasks:
    - name: Include deploy role
      ansible.builtin.include_role:
        name: network.base.deploy
      vars:
        resources:
          - 'interfaces'
          - 'l2_interfaces'
        data_store:
          scm:
            origin:
              url: "{{ gh_scm_path }}"
              token: "{{ gh_token }}"
              user:
                name: "{{ gh_username }}"
                email: "{{ gh_user_email }}"
```
Example Output
When the playbook is executed successfully, the output will show the configurations being deployed when output verbosity is debug mode.

## License

GNU General Public License v3.0 or later.

See [LICENSE](https://www.gnu.org/licenses/gpl-3.0.txt) to see the full text.

## Author Information

- Ansible Network Content Team
