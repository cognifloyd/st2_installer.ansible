# st2_installer.ansible

Ansible Collection for installing StackStorm (ST2)

## Goals for this collection

These goals are mix of things to acomplish and guidelines on the scope of this project.

- Reach feature parity with other st2 installers.
- Speed up development (as compared with StackStorm/ansible-st2).
- Participate in the wider ansible community; Use and contribute to 3rd party collections and roles wherever possible.
- Make the installer a proper Ansible collection.
- Support upgrading st2 installations.
- Make it easier to scale-up StackStorm installations. As the installation scales up, the roles should facilitate moving components into HA setups.
- Use molecule to test the roles (molecule is a standard testing tool in the ansible community).
- Use [pulp_installer](https://github.com/pulp/pulp_installer/) is a good example of how to structure a collection like this.

## Relationship with StackStorm/ansible-st2

This collection replaces [StackStorm/ansible-st2](https://github.com/StackStorm/ansible-st2).
ansible-st2 is missing many of the features of the other st2 installation methods, especially
security features like securing mongodb and rabbitmq installations. It is difficult to add
features when so many parts of StackStorm's internal infrastructure dogfood (use) ansible-st2.
So, the safest way to add these features, as well as turning the installer into a proper
ansible collection for ansible 2.9+, was to fork ansible-st2.
