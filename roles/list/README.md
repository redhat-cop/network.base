# list

## Overview
The `list` role is designed to retrieve and display a  list of supported network resource modules for provided OS. This role helps administrators gain visibility into the network by providing information in a standardized format.

## Features
- Retrieve a list of supported network resource modules.

## Variables

| Variable Name          | Default Value | Required | Type | Description | Example |
|:-----------------------|:-------------:|:--------:|:----:|:------------|:-------:|
| `ansible_network_os` | `""`          | no      | str  | Network OS to be used during listing.                    | `"cisco.ios.ios"` |


## Usage
Below is an example playbook demonstrating how to use the `list` role.

```yaml
- name: Retrieve and display supported network resource modules
  hosts: all
  tasks:
    - name: Include the list role
      ansible.builtin.include_role:
        name: network.base.list
```
Example Output
If output_format is set to yaml, the output will look like this:

```yaml
    ansible_connection: ansible.netcommon.network_cli
    ansible_network_os: cisco.ios.ios
    changed: false
    failed: false
    modules:
    - acl_interfaces
    - acls
    - bgp_address_family
    - bgp_global
    - evpn_evi
    - evpn_global
    - hostname
    - interfaces
    - l2_interfaces
    - l3_interfaces
    - lacp
    - lacp_interfaces
    - lag_interfaces
    - lldp_global
    - lldp_interfaces
    - logging_global
    - ntp_global
    - ospf_interfaces
    - ospfv2
    - ospfv3
    - prefix_lists
    - route_maps
    - service
    - snmp_server
    - static_routes
    - vlans
    - vxlan_vtep
```
## License

GNU General Public License v3.0 or later.

See [LICENSE](https://www.gnu.org/licenses/gpl-3.0.txt) to see the full text.

## Author Information

- Ansible Network Content Team
