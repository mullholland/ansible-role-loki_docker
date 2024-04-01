# [Ansible role loki_docker](#loki_docker)

Installs and configures loki container based on official loki docker container

|GitHub|Downloads|Version|
|------|---------|-------|
|[![github](https://github.com/mullholland/ansible-role-loki_docker/actions/workflows/molecule.yml/badge.svg)](https://github.com/mullholland/ansible-role-loki_docker/actions/workflows/molecule.yml)|[![downloads](https://img.shields.io/ansible/role/d/mullholland/loki_docker)](https://galaxy.ansible.com/mullholland/loki_docker)|[![Version](https://img.shields.io/github/release/mullholland/ansible-role-loki_docker.svg)](https://github.com/mullholland/ansible-role-loki_docker/releases/)|
## [Example Playbook](#example-playbook)

This example is taken from [`molecule/default/converge.yml`](https://github.com/mullholland/ansible-role-loki_docker/blob/master/molecule/default/converge.yml) and is tested on each push, pull request and release.

```yaml
---
- name: Converge
  hosts: all
  become: true
  gather_facts: true
  vars:
    adguardhome_docker_config:
      tls:
        options:
          modern:
            minVersion: "VersionTLS13"
            sniStrict: true

  roles:
    - role: "mullholland.loki_docker"
```

The machine needs to be prepared. In CI this is done using [`molecule/default/prepare.yml`](https://github.com/mullholland/ansible-role-loki_docker/blob/master/molecule/default/prepare.yml):

```yaml
---
- name: Prepare
  hosts: all
  become: true
  gather_facts: true
  vars:
    pip_packages:
      - "docker"

  roles:
    - role: mullholland.docker
    - role: mullholland.repository_epel
    - role: mullholland.pip
```



## [Role Variables](#role-variables)

The default values for the variables are set in [`defaults/main.yml`](https://github.com/mullholland/ansible-role-loki_docker/blob/master/defaults/main.yml):

```yaml
---
# General config
loki_docker_network_name: "web"
loki_docker_base_path: "/opt"
loki_docker_timezone: "Europe/Berlin"

# User/Group of the stack. Everything is mapped to this, instead of root.
loki_docker_user: "homelab"
loki_docker_uid: "900"
loki_docker_group: "homelab"
loki_docker_gid: "900"
loki_docker_user_system: true

# which container version to install
# https://hub.docker.com/r/loki/loki/tags
# can also be latest
loki_docker_version: "grafana/loki:latest"

# additional docker compose environment varaibles
# https://loki.com/docs/loki/latest/setup-loki/configure-loki/#override-configuration-with-environment-variables
loki_docker_environment_variables: []

loki_docker_volumes:
  - "/opt/loki/conf/config.yaml:/etc/loki/local-config.yaml"
  - "/opt/loki/data:/data"

# which port to expose. can be empty
loki_docker_ports:
  - "3100:3100/tcp"  # WebUI

loki_docker_labels:
  - "traefik.enable=false"
# - "traefik.enable=true"
# - "traefik.http.routers.loki.entryPoints=https"
# - "traefik.http.routers.loki.rule=Host(`loki.example.com`)"
# - "traefik.http.routers.loki.tls.certResolver=letsEncrypt"
# - "traefik.http.routers.loki.tls=true"
#  - "traefik.http.routers.loki.service=loki"
#  - "traefik.http.routers.loki.loadbalancer.server.port=80"

loki_docker_config:
  auth_enabled: false
  server:
    http_listen_port: 3100
    grpc_listen_port: 9096
  common:
    instance_addr: 127.0.0.1
    path_prefix: /data/loki
    storage:
      filesystem:
        chunks_directory: /data/loki/chunks
        rules_directory: /data/loki/rules
    replication_factor: 1
    ring:
      kvstore:
        store: inmemory
  query_range:
    results_cache:
      cache:
        embedded_cache:
          enabled: true
          max_size_mb: 100
  schema_config:
    configs:
      - from: 2020-10-24
        store: boltdb-shipper
        object_store: filesystem
        schema: v11
        index:
          prefix: index_
          period: 24h
  ruler:
    alertmanager_url: http://localhost:9093
  analytics:
    reporting_enabled: false
```

## [Requirements](#requirements)

- pip packages listed in [requirements.txt](https://github.com/mullholland/ansible-role-loki_docker/blob/master/requirements.txt).

## [State of used roles](#state-of-used-roles)

The following roles are used to prepare a system. You can prepare your system in another way.

| Requirement | GitHub | GitLab |
|-------------|--------|--------|
|[mullholland.repository_epel](https://galaxy.ansible.com/mullholland/repository_epel)|[![Build Status GitHub](https://github.com/mullholland/ansible-role-repository_epel/workflows/Ansible%20Molecule/badge.svg)](https://github.com/mullholland/ansible-role-repository_epel/actions)|[![Build Status GitLab](https://gitlab.com/opensourceunicorn/ansible-role-repository_epel/badges/master/pipeline.svg)](https://gitlab.com/opensourceunicorn/ansible-role-repository_epel)|
|[mullholland.docker](https://galaxy.ansible.com/mullholland/docker)|[![Build Status GitHub](https://github.com/mullholland/ansible-role-docker/workflows/Ansible%20Molecule/badge.svg)](https://github.com/mullholland/ansible-role-docker/actions)|[![Build Status GitLab](https://gitlab.com/opensourceunicorn/ansible-role-docker/badges/master/pipeline.svg)](https://gitlab.com/opensourceunicorn/ansible-role-docker)|
|[mullholland.pip](https://galaxy.ansible.com/mullholland/pip)|[![Build Status GitHub](https://github.com/mullholland/ansible-role-pip/workflows/Ansible%20Molecule/badge.svg)](https://github.com/mullholland/ansible-role-pip/actions)|[![Build Status GitLab](https://gitlab.com/opensourceunicorn/ansible-role-pip/badges/master/pipeline.svg)](https://gitlab.com/opensourceunicorn/ansible-role-pip)|

## [Context](#context)

This role is a part of many compatible roles. Have a look at [the documentation of these roles](https://mullholland.net) for further information.

Here is an overview of related roles:
![dependencies](https://raw.githubusercontent.com/mullholland/ansible-role-loki_docker/png/requirements.png "Dependencies")

## [Compatibility](#compatibility)

This role has been tested on these [container images](https://hub.docker.com/u/mullholland):

|container|tags|
|---------|----|
|[EL](https://hub.docker.com/r/mullholland/enterpriselinux)|all|
|[Fedora](https://hub.docker.com/r/mullholland/fedora/)|38, 39|
|[Ubuntu](https://hub.docker.com/r/mullholland/ubuntu)|all|
|[Debian](https://hub.docker.com/r/mullholland/debian)|all|

The minimum version of Ansible required is 2.10, tests have been done to:

- The previous version.
- The current version.
- The development version.

If you find issues, please register them in [GitHub](https://github.com/mullholland/ansible-role-loki_docker/issues).

## [License](#license)

[MIT](https://github.com/mullholland/ansible-role-loki_docker/blob/master/LICENSE).

## [Author Information](#author-information)

[mullholland](https://mullholland.net)
