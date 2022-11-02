## Network Base Validated Content

### Overview

The `network.base` application acts as core for other validated content, as it provides the platform agnostic role called Resource Manager.
This validated content provides a single platform-agnostics entry point to manage all the resources supported for a given network OS.

**Capabilities**
- Build Brownfield Inventory: This helps user to be able to get the facts for a given resource and store it as host_vars thus enabling the capability to get facts for all the hosts within the inventory and store facts in a structured format which acts as SOT.
- Supported Resource Query: This helps user to be able to get the list of resource modules supported for a given network os.
- Gather: This helps user to be able to gather the facts for specified network resources.
- Persist: This helps user to be able to gather the facts for specified network resources and persist those facts into inventory files as host vars.
- Deploy: This helps user to be able to deploy the confguration persisted in terms of host vars on to the appliance.
- Configure: This helps user to be able to apply configuration in a normalized manner the way resource module does.

### Usage
This platform agnostic role enables the user to create a runtime brownfield inventory with all the configuration in terms of host vars.
These host vars are ansible facts which have been gathered through the network resource module.The tasks offered by this role could be observed as below:

#### Supported Resource Query
```yaml

---
- hosts: ios
  tasks:
  - name: invoke list function
    include_role:
      name: resource_manager
    vars:
      ansible_network_os: cisco.ios.ios
      action: list
```


#### Building Brownfield Inventory
```

---
- hosts: ios
  gather_facts: false
  tasks:
  - name: invoke persist function
    include_role:
      name: resource_manager
    vars:
      action: persist
      ansible_network_os: cisco.ios.ios
      resources:
        - 'interfaces'
        - 'l2_interfaces'
        - 'l3_interfaces'
```

#### Gather BGP Facts
```

---
- hosts: ios
  gather_facts: false
  tasks:
  - name: invoke gather function
    include_role:
      name: resource_manager
    vars:
      action: gather
      ansible_network_os: cisco.ios.ios
      resources:
        - 'interfaces'
        - 'l2_interfaces'
        - 'l3_interfaces'
```

#### Deploy BGP Configuration
```

- hosts: ios
  gather_facts: false
  tasks:
  - name: invoke deploy function
    include_role:
      name: resource_manager
    vars:
      action: gather
      ansible_network_os: cisco.ios.ios
      resources:
        - 'interfaces'
        - 'l2_interfaces'
        - 'l3_interfaces'
```
