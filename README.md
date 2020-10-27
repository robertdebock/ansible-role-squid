# [squid](#squid)

Install and configure squid on your system

|Travis|GitHub|Quality|Downloads|Version|
|------|------|-------|---------|-------|
|[![travis](https://travis-ci.com/robertdebock/ansible-role-squid.svg?branch=master)](https://travis-ci.com/robertdebock/ansible-role-squid)|[![github](https://github.com/robertdebock/ansible-role-squid/workflows/Ansible%20Molecule/badge.svg)](https://github.com/robertdebock/ansible-role-squid/actions)|[![quality](https://img.shields.io/ansible/quality/35939)](https://galaxy.ansible.com/robertdebock/squid)|[![downloads](https://img.shields.io/ansible/role/d/35939)](https://galaxy.ansible.com/robertdebock/squid)|[![Version](https://img.shields.io/github/release/robertdebock/ansible-role-squid.svg)](https://github.com/robertdebock/ansible-role-squid/releases/)|

## [Example Playbook](#example-playbook)

This example is taken from `molecule/resources/converge.yml` and is tested on each push, pull request and release.
```yaml
---
- name: Converge
  hosts: all
  become: yes
  gather_facts: yes

  roles:
    - role: robertdebock.squid
      squid_port: 3128
      squid_cache_dir: aufs /var/spool/squid 16000 16 256 max-size=8589934592
      squid_cache_replacement_policy: heap LFUDA
      squid_maximum_object_size_mb: 256
```

Example with http_access.
```yaml
---
- name: Allow access to Ubuntu repos from production subnet
  hosts: all
  become: yes
  gather_facts: yes
  roles:
    - role: robertdebock.squid
      squid_acls:
        - name: ubuntu
          classifier: dstdomain
          value: 'security.ubuntu.com eu-west-1.ec2.archive.ubuntu.com'
        - name: production
          classifier: src
          value: '10.10.10.0/24'
      squid_http_access:
        - state: allow
          acls:
            - production
            - ubuntu
```

The machine may need to be prepared using `molecule/resources/prepare.yml`:
```yaml
---
- name: Prepare
  hosts: all
  gather_facts: no
  become: yes

  roles:
    - role: robertdebock.bootstrap
    - role: robertdebock.core_dependencies
```

For verification `molecule/resources/verify.yml` runs after the role has been applied.
```yaml
---
- name: Verify
  hosts: all
  become: yes
  gather_facts: no

  tasks:
    - name: check if connection still works
      ping:
```

Also see a [full explanation and example](https://robertdebock.nl/how-to-use-these-roles.html) on how to use these roles.

## [Role Variables](#role-variables)

These variables are set in `defaults/main.yml`:
```yaml
---
# defaults file for squid

squid_port: 3128

squid_cache_dir: ufs /var/spool/squid 100 16 256

# This is the default set of ACLS.
squid_acls:
  - name: localhost
    classifier: src
    value: 127.0.0.0/8
  - name: localnet
    classifier: src
    value: 10.0.0.0/8
  - name: localnet
    classifier: src
    value: 172.16.0.0/12
  - name: localnet
    classifier: src
    value: 192.168.0.0/16
  - name: localnet
    classifier: src
    value: "fc00::/7"
  - name: localnet
    classifier: src
    value: "fe80::/10"
  - name: SSL_ports
    classifier: port
    value: 443
  - name: Safe_ports
    classifier: port
    value: 80
  - name: Safe_ports
    classifier: port
    value: 21
  - name: Safe_ports
    classifier: port
    value: 443
  - name: Safe_ports
    classifier: port
    value: 70
  - name: Safe_ports
    classifier: port
    value: 210
  - name: Safe_ports
    classifier: port
    value: 1025-65535
  - name: Safe_ports
    classifier: port
    value: 280
  - name: Safe_ports
    classifier: port
    value: 488
  - name: Safe_ports
    classifier: port
    value: 591
  - name: Safe_ports
    classifier: port
    value: 777
  - name: CONNECT
    classifier: method
    value: CONNECT

# This is how to allow (or deny) access to specified ACLs.
squid_cache_rules:
  - acl: "!Safe_ports"
    decision: deny
  - acl: "CONNECT !SSL_Ports"
    decision: deny
  - acl: localhost
    decision: allow
  - acl: localnet
    decision: allow
  - acl: all
    decision: deny
```

## [Requirements](#requirements)

- Access to a repository containing packages, likely on the internet.
- A recent version of Ansible. (Tests run on the current, previous and next release of Ansible.)

The following roles can be installed to ensure all requirements are met, using `ansible-galaxy install -r requirements.yml`:

```yaml
---
- robertdebock.bootstrap
- robertdebock.core_dependencies

```

## [Context](#context)

This role is a part of many compatible roles. Have a look at [the documentation of these roles](https://robertdebock.nl/) for further information.

Here is an overview of related roles:
![dependencies](https://raw.githubusercontent.com/robertdebock/drawings/artifacts/squid.png "Dependency")

## [Compatibility](#compatibility)

This role has been tested on these [container images](https://hub.docker.com/u/robertdebock):

|container|tags|
|---------|----|
|alpine|all|
|amazon|2018.03|
|el|7, 8|
|debian|buster, bullseye|
|fedora|31, 32|
|opensuse|all|
|ubuntu|focal, bionic, xenial|

The minimum version of Ansible required is 2.9, tests have been done to:

- The previous version.
- The current version.
- The development version.



## [Testing](#testing)

[Unit tests](https://travis-ci.com/robertdebock/ansible-role-squid) are done on every commit, pull request, release and periodically.

If you find issues, please register them in [GitHub](https://github.com/robertdebock/ansible-role-squid/issues)

Testing is done using [Tox](https://tox.readthedocs.io/en/latest/) and [Molecule](https://github.com/ansible/molecule):

[Tox](https://tox.readthedocs.io/en/latest/) tests multiple ansible versions.
[Molecule](https://github.com/ansible/molecule) tests multiple distributions.

To test using the defaults (any installed ansible version, namespace: `robertdebock`, image: `fedora`, tag: `latest`):

```
molecule test

# Or select a specific image:
image=ubuntu molecule test
# Or select a specific image and a specific tag:
image="debian" tag="stable" tox
```

Or you can test multiple versions of Ansible, and select images:
Tox allows multiple versions of Ansible to be tested. To run the default (namespace: `robertdebock`, image: `fedora`, tag: `latest`) tests:

```
tox

# To run CentOS (namespace: `robertdebock`, tag: `latest`)
image="centos" tox
# Or customize more:
image="debian" tag="stable" tox
```

## [License](#license)

Apache-2.0

## [Contributors](#contributors)

I'd like to thank everybody that made contributions to this repository. It motivates me, improves the code and is just fun to collaborate.

- [meons](https://github.com/meons)

## [Author Information](#author-information)

[Robert de Bock](https://robertdebock.nl/)

Please consider [sponsoring me](https://github.com/sponsors/robertdebock).
