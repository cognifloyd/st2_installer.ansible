# Ansible Collection - cognifloyd.st2_installer (st2_installer.ansible)

Ansible Collection for installing StackStorm (ST2)

_This is not an official part of the StackStorm project._

> [StackStorm](http://stackstorm.com/) is event-driven automation platform written in Python.
With over [50+ integrations](https://exchange.stackstorm.org) like GitHub, Docker, Nagios, NewRelic, AWS, Ansible it allows you to wire together your existing infrastructure into complex Workflows with auto-remediation and many more.
Aka IFTTT orchestration for Ops.

[![Build Status](https://travis-ci.org/cognifloyd/st2_installer.ansible.svg?branch=master)](https://travis-ci.org/cognifloyd/st2_installer.ansible)
[![Repository deb/rpm](https://img.shields.io/badge/Repository-deb/rpm-blue.svg)](https://packagecloud.io/StackStorm/stable/)
[![Join our community Slack](https://stackstorm-community.herokuapp.com/badge.svg)](https://stackstorm.com/community-signup)

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

<!-- TODO: Deal with any inaccuracies in the readme sections below from ansible-st2 -->

## Supported platforms
* Ubuntu Bionic (18.04)
* Ubuntu Focal (20.04)
* RHEL7 / CentOS7
* RHEL8 / CentOS8

<!-- > If you're using the provided Vagrantfile, note that it uses Bionic by default. -->

> In order to access StackStorm Web UI, please don't forget to ensure that http/https ports are opened in your firewall system.

## Requirements
At least 2GB of memory and 3.5GB of disk space is required, since StackStorm is shipped with RabbitMQ, Mongo, Redis  and nginx.

## Installation
```sh
ansible-galaxy collection install cognifloyd.st2_installer
```

## Variables
Below is the list of variables you can redefine in your playbook to customize st2 deployment:

| Variable                 | Default       | Description  |
| ------------------------ | ------------- | ------------ |
| **st2repo**
| `st2repo_name`           | `stable`      | StackStorm PackageCloud repository to install. [`stable`](https://packagecloud.io/StackStorm/stable/), [`unstable`](https://packagecloud.io/StackStorm/unstable/), [`staging-stable`](https://packagecloud.io/StackStorm/staging-stable/), [`staging-unstable`](https://packagecloud.io/StackStorm/staging-unstable/)
| **st2**
| `st2_version`            | `latest`      | StackStorm version to install. `present` to install available package, `latest` to get automatic updates, or pin it to numeric version like `2.2.0` or with revision like `2.2.0-1`
| `st2_config`             | `{}`          | Hash with StackStorm configuration settings to set in [`st2.conf`](https://github.com/StackStorm/st2/blob/master/conf/st2.conf.sample) ini file.
| `st2_system_user`        | `stanley`     | System user from which st2 will execute local/remote shell actions.
| `st2_system_user_in_sudoers` | `yes`| Add `st2_system_user` to the sudoers (recommended for most `st2` features to work).
| `st2_ssh_key_file`       | `/home/{{st2_system_user}}/.ssh/{{st2_system_user}}_rsa` | Path to `st2_system_user` SSH private key. It will be autogenerated by default.
| `st2_auth_enable`        | `yes`         | Enable StackStorm standalone authentication.
| `st2_auth_username`      | `testu`       | Username used by StackStorm standalone authentication.
| `st2_auth_password`      | `testp`       | Password used by StackStorm standalone authentication.
| `st2_save_credentials`   | `yes`         | Save credentials for local CLI in `/root/.st2/config` file.
| `st2_rbac_enable`        | `no`          | Enable RBAC. |
| `st2_rbac`               | [See `st2_rbac` variable in role defaults](roles/st2/defaults/main.yml) | RBAC roles and assignments. This is a dictionary with two keys `roles` and `assignments`. `roles` and `assignments` are in turn both arrays. Each element in the array follows the exact YAML schema for [roles](https://docs.stackstorm.com/rbac.html#user-permissions) and [assignments](https://docs.stackstorm.com/rbac.html#defining-user-role-assignments) defined in ST2 documentation.
| `st2_ldap_enable`        | `no`          | Enable LDAP authentication backend. |
| `st2_ldap`               | [See `st2_ldap` variable in role defaults](roles/st2/defaults/main.yml) | Settings for LDAP authentication backend. `st2_ldap` is a dictionary and has one item `backend_kwargs`. `backend_kwargs` should be provided as exactly listed in ST2 documentation for [LDAP configuration](https://docs.stackstorm.com/authentication.html#ldap).
| `st2_packs`              | `[ st2 ]`     | List of packs to install. This flag does not work with a `--python3` only pack.
| `st2_python_packages`    | `[ ]`         | List of python packages to install into the `/opt/stackstorm/st2` virtualenv. This is needed when deploying alternative auth or coordination backends which depend on Python modules to make them work.
| **st2web**
| `st2web_version`     | `latest`      | st2web version to install. `present` to install available package, `latest` to get automatic updates, or pin it to numeric version like `2.2.0` or with revision like `2.2.0-1`.
| `st2web_ssl_certificate`     | `null` | String with custom SSL certificate (`.crt`). If not provided, self-signed certificate will be generated.
| `st2web_ssl_certificate_key` | `null` | String with custom SSL certificate secret key (`.key`). If not provided, self-signed certificate will be generated.
| `st2web_nginx_config`     | `null` | String with a custom nginx configuration file (`st2.conf`). If not provided, the default st2.conf will be used.
| **st2chatops**
| `nodejs_major_version`   | `14`          | The default fits st2chatops version >= 3.5.0.
| `st2chatops_version`     | `latest`      | st2chatops version to install. `present` to install available package, `latest` to get automatic updates, or pin it to numeric version like `2.2.0` or with revision like `2.2.0-1`.
| `st2chatops_st2_api_key` |               | st2 API key to be updated in st2chatops.env using "st2 apikey create -k" in a task
| `st2chatops_hubot_adapter` |             | Hubot Adapter to be used for st2chatops. Default is `shell`, but should be changed to one of the [`supported adapters`](`https://github.com/StackStorm/ansible-st2/blob/master/roles/st2chatops/vars/main.yml`).[**Required**]
| `st2chatops_config`      | `{ }`         | Based on adapter in `st2chatops_hubot_adapter`, provide hash for the adapter settings, to update [`st2chatops.env`](https://github.com/StackStorm/st2chatops/blob/master/st2chatops.env). For example, for `Slack` hubot adapter: `st2chatops_config:` `HUBOT_SLACK_TOKEN: xoxb-CHANGE-ME-PLEASE`
| `st2chatops_version`     | `latest`      | st2chatops version to install. Use `latest` to get automatic updates or pin it to numeric version like `2.2.0`.

<!-- ## Examples
Install latest `stable` StackStorm with all its components on local machine:
```sh
ansible-playbook stackstorm.yml -i 'localhost,' --connection=local
```

> Note that keeping `latest` version is useful to update StackStorm by re-running playbook, since it will reinstall st2 if there is new version available.
This is default behavior. If you don't want updates - consider pinning version-revision numbers.

Install specific numeric version of st2 with pinned revision number as well:
```sh
ansible-playbook stackstorm.yml --extra-vars='st2_version=2.2.0 st2_revision=8'
```
-->

## Installing behind a proxy.

If you are installing from behind a proxy, you can use environment variables `http_proxy`, `https_proxy`, and `no_proxy` in the playbook. For the
st2smoketests, you will need to disable proxy for localhost.

```yaml
  environment:
    http_proxy: http://proxy.example.net:3128
    https_proxy: http://proxy.example.net:3128
    no_proxy: 127.0.0.1,localhost
```

## Developing
There are a few requirements when developing on `st2_installer.ansible`.

These are the platforms we must support (must pass end-to-end testing):
- Ubuntu Bionic
- Ubuntu Focal
- CentOS7
- CentOS8
- RHEL7 (via AWS)
- RHEL8 (via AWS)

Must also support Ansible Idempotence (Eg. Ansible-playbook re-run should end with the following results: `changed=0.*failed=0`)

<!-- For development purposes there is [Vagrantfile](Vagrantfile) available. The following command will setup ubuntu18 box (`ubuntu/bionic64`) by default:
```sh
vagrant up
```

Other distros:
```sh
vagrant up ubuntu20
vagrant up centos7
vagrant up centos8
```
-->

## Other Installers
You might be interested in other methods to deploy StackStorm engine:
* Configuration Management
  * [Puppet Module](https://github.com/stackstorm/puppet-st2)
  * [Official Ansible Roles](https://github.com/stackstorm/ansible-st2)

* Manual Instructions
  * [Ubuntu 18.04](https://docs.stackstorm.com/install/u18.html)
  * [Ubuntu 20.04](https://docs.stackstorm.com/install/u20.html)
  * [RHEL8/CentOS8](https://docs.stackstorm.com/install/rhel8.html)
  * [RHEL7/CentOS7](https://docs.stackstorm.com/install/rhel7.html)

## Help
If you're in stuck, our community always ready to help, feel free to:
<!-- * Ask questions in our [public Slack channel](https://stackstorm.com/community-signup) -->
* [Report bug](https://github.com/cognifloyd/st2_installer.ansible/issues), provide [feature request](https://github.com/cognifloyd/st2_installer.ansible/pulls) or just give us a ✮ star

Your contribution is more than welcome!
