# ansible-role-docker

Installs Docker from official Docker binaries archive (no PPA or apt repository). For managing Docker daemon systemd is used. Should work with basically every Linux OS using `systemd`.

## Versions

I tag every release and try to stay with [semantic versioning](http://semver.org). If you want to use the role I recommend to checkout the latest tag. The master branch is basically development while the tags mark stable releases. But in general I try to keep master in good shape too. A tag `11.2.0+26.1.0` means this is release `11.2.0` of this role and it's meant to be used with Docker version `26.1.0`. If the role itself changes `X.Y.Z` before `+` will increase. If the Docker version changes `XX.YY.ZZ` after `+` will increase. This allows to tag bugfixes and new major versions of the role while it's still developed for a specific Docker release.

## Changelog

see [Changelog](https://github.com/githubixx/ansible-role-docker/blob/master/CHANGELOG.md)

## Role Variables

```yaml
# Directory to store downloaded Docker archive and unarchived binary files.
docker_download_dir: "/opt/tmp"

# Docker version to download and use.
docker_version: "26.1.0"
docker_user: "docker"
docker_group: "docker"
docker_uid: 666
docker_gid: 666

# Directory to store Docker binaries. Should be in your search PATH!
docker_bin_dir: "/usr/local/bin"

# For Archlinux the values of this variable can either be "iptables" or
# "nftables". For all other OSes "iptables" is a requirement as Docker
# depends on "iptables" command. In case of Archlinux "nftables" also
# includes "iptables" so both work.
# 
# Ubuntu 20.04 and Debian 10 only provides "iptables".
#
# Ubuntu 22.04, 22.04, Debian 11 and 12 allows to install "iptables" and "nftables"
# in parallel.
#
# So for Archlinux if either "iptables" or "iptables-nft" package is
# already installed this role won't change anything. For all other OSes
# "iptables" package is a requirement. So even if "nftables" package is
# already installed this role will install "iptables" package.
#
# Possible values:
# - iptables # Possible for all supported OSes
# - nftables # Only for Archlinux
docker_firewall_flavor: "iptables"

# Settings for "dockerd" daemon. Will be provided as parameter to "dockerd" in
# systemd service file for Docker. These variables and it's values can be
# overridden with `dockerd_settings_user` variable. Also additional variables
# can be added of course. For possible values see:
# https://docs.docker.com/engine/reference/commandline/dockerd/#daemon
dockerd_settings:
  "host": "unix:///run/docker.sock"
  "log-level": "info"
  "storage-driver": "overlay2"
  "iptables": "true"
  "ip-masq": "true"
  "mtu": "1500"

# To override settings defined in `dockerd_settings` this variable can be
# used. Of course additional variables can be added too. The example below
# would add the "--debug=true" switch to `dockerd` e.g. For possible values
# see:
# https://docs.docker.com/engine/reference/commandline/dockerd/#daemon
# dockerd_settings_user:
#   "debug": "true"

# The directory from where to copy the Docker CA certificates. By default this
# will expand to user's LOCAL $HOME (the user that run's "ansible-playbook ..."
# plus "/docker-ca-certificates". That means if the user's $HOME directory is
# e.g. "/home/da_user" then "docker_ca_certificates_src_dir" will have a value
# of "/home/da_user/docker-ca-certificates".
docker_ca_certificates_src_dir: "{{ '~/docker-ca-certificates' | expanduser }}"

# The directory where the program "update-ca-certificates" searches for CA
# certificate files (besides other locations).
docker_ca_certificates_dst_dir: "/usr/local/share/ca-certificates"

# Currently only "standalone" is supported. So that means on the remote host
# "docker-compose" command will be available and not the "docker compose"
# plugin (without "-").
# When commented no "docker-compose" will be installed and all "docker_compose_*"
# variables are ignored.
#docker_compose_type: "standalone"

# "docker-compose" version
docker_compose_version: "2.27.0"

# The directory where to "docker-compose" binary will be installed
docker_compose_bin_directory: "/usr/local/bin"

# Owner of the "docker-compose" binary
docker_compose_bin_owner: "root"

# Group of the "docker-compose" binary
docker_compose_bin_group: "root"

# File permissions of "docker-compose" binary
docker_compose_bin_file_perm: "0755"
```

Variables with no defaults:

```yaml
# If you've a Docker registry with a self signed certificate you can copy the
# certificate authority (CA) file to the remote host to the CA certificate store.
# This way Docker will trust the SSL certificate of your Docker registry.
# It's important to mention that the CA files needs a ".crt" extension!
# "docker_ca_certificates" is a list so you can specify as much CA files as
# you want. The Ansible role will lookup for the files specified here in
# "docker_ca_certificates_src_dir" (see above). If "docker_ca_certificates"
# is not specified the task will be ignored.
docker_ca_certificates:
  - ca-docker.crt
```

The settings for `dockerd` daemon defined in `dockerd_settings` can be overridden by defining a variable called `dockerd_settings_user`. You can also add additional settings by using this variable. E.g. if you add the following variables and their values to `group_vars/all.yml` (or where ever it fit's best for you) `dockerd` the default settings will be overridden (see above):

```yaml
dockerd_settings_user:
  "host": "unix:///var/run/docker.sock"
  "log-level": "error"
  "storage-driver": "aufs"
  "iptables": "false"
  "ip-masq": "false"
  "mtu": "1400"
```

Of course you can add more settings.

## Upgrading Docker

If you want upgrade Docker update `docker_version` variable accordingly. Afterwards if you run `ansible-playbook` and supply the argument `--extra-vars="upgrade_docker=true"` the playbook will download the specified Docker version and installs the binaries. This will cause systemd to restart `docker.service`. To avoid restarting all Docker daemons on all of your hosts at once consider using `--limit` parameter or reduce parallel Ansible tasks with `--forks`.

## Example Playbook

```yaml
- hosts: docker_hosts
  roles:
    - githubixx.docker
```

## Testing

This role has a small test setup that is created using [Molecule](https://github.com/ansible-community/molecule), libvirt (vagrant-libvirt) and QEMU/KVM. Please see my blog post [Testing Ansible roles with Molecule, libvirt (vagrant-libvirt) and QEMU/KVM](https://www.tauceti.blog/posts/testing-ansible-roles-with-molecule-libvirt-vagrant-qemu-kvm/) how to setup. The test configuration is [here](https://github.com/githubixx/ansible-role-docker/tree/master/molecule/default).

Afterwards molecule can be executed:

```bash
molecule converge
```

This will setup a few virtual machines (VM) with different supported Linux operating systems and installs `docker` role.

To run a few tests:

```bash
molecule verify
```

To clean up run

```bash
molecule destroy
```

## License

GNU GENERAL PUBLIC LICENSE Version 3

## Author Information

[http://www.tauceti.blog](http://www.tauceti.blog)
