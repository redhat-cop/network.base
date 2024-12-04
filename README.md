# Ansible Network Base
[![CI](https://github.com/ansible-network/network.base/actions/workflows/tests.yml/badge.svg?event=schedule)](https://github.com/ansible-network/network.base/actions/workflows/tests.yml)
[![OpenSSF Best Practices](https://bestpractices.coreinfrastructure.org/projects/7404/badge)](https://bestpractices.coreinfrastructure.org/projects/7404)

## About
The Ansible Network Base Validated Content provides essential roles to manage network devices effectively. This content is designed to be platform-agnostic and supports a wide range of networking platforms, making it a versatile toolkit for network administrators and engineers.

This collection includes the following roles:
- **`list`**: Retrieve and display supported resources that can be managed.
- **`configure`**: Apply, validate, and manage network configurations.
- **`deploy`**: Ensure consistent configuration deployment across network devices.
- **`detect`**: Identify configuration drifts between desired and actual states.
- **`remediate`**: Automatically correct configuration drifts and restore compliance.
- **`gather`**: Collect facts and running configurations from network devices.
- **`persist`**: Save network device configurations and facts to local or remote repositories for backup or audit purposes.

## Included content

Click on the name of a role to view its documentation:

<!--start collection content-->
### Roles
Name | Description
--- | ---
[network.base.list](roles/list/README.md) | Retrieve and display supported manageable resources.
[network.base.configure](roles/configure/README.md) | Apply, validate, and manage network configurations.
[network.base.deploy](roles/deploy/README.md) | Deploy consistent network configurations.
[network.base.detect](roles/detect/README.md) | Identify configuration drifts and discrepancies.
[network.base.remediate](roles/remediate/README.md) | Correct configuration drifts and restore compliance.
[network.base.gather](roles/gather/README.md) | Collect facts and running configurations from network devices.
[network.base.persist](roles/persist/README.md) | Save configurations and facts to local or remote repositories.
<!--end collection content-->

## Requirements
- [Requires Ansible](https://github.com/ansible-network/network.base/blob/main/meta/runtime.yml)
- [Requires Content Collections](https://github.com/ansible-network/network.base/blob/main/galaxy.yml)
- [Testing Requirements](https://github.com/ansible-network/network.base/blob/main/test-requirements.txt)
- Supported platform collections:
  - [arista.eos](https://github.com/ansible-collections/arista.eos) >= v9.0.0
  - [cisco.ios](https://github.com/ansible-collections/cisco.ios) >= v8.0.0
  - [cisco.iosxr](https://github.com/ansible-collections/cisco.iosxr) >= v9.0.0
  - [cisco.nxos](https://github.com/ansible-collections/cisco.nxos) >= v8.0.0
  - [junipernetworks.junos](https://github.com/ansible-collections/junipernetworks.junos) >= v8.0.0

## Installation
To consume this Validated Content from Automation Hub, add the following configuration to your `ansible.cfg`:

```ini
[galaxy]
server_list = automation_hub

[galaxy_server.automation_hub]
url=https://console.redhat.com/api/automation-hub/content/validated/
auth_url=https://sso.redhat.com/auth/realms/redhat-external/protocol/openid-connect/token
token=<SuperSecretToken>
```
Utilize the current Token, and if the token has expired, obtain the necessary
token from the [Automation Hub Web UI](https://console.redhat.com/ansible/automation-hub/token).

With this configured, simply run the following commands:

```
ansible-galaxy collection install network.base
```

## Use Cases
This collection includes platform-agnostic role that enables the users to perform following .

`Build Brownfield Inventory`:
- The `persist` role enables users to fetch facts for provided resources and persist these YAML formatted structured host-vars either to local or remote data stores which could act as a single SOT.

`Configuration Deployment`:
- The `deploy` role enables a user to read the host_vars from a local or remote data store and deploys if any changes are found.

`Display Structured Configuration`:
- The `gather` role enables users to be able to gather and display the structured facts for provided network resources.

`Configuration Drift`:
- The `detect` role will read the facts from the default/local or remote inventory host_vars and detect if any configuration changes are there between running and the provided config configuration.

`Remediate Configuration`:
- The `remediate` role will read the facts from the provided/default or remote inventory and remediate if there are any configuration changes on the appliances. This is done by overriding the running configuration with read facts from the provided inventory host vars.

`Supported Resource Modules Query`:
- The `list` role enables users to get the list of supported resource modules for the provided network OS.

`Configure`:
- The `configure` role enables the user to apply configuration in a normalized manner the way resource modules do.

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
