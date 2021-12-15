ansible-role-docker
===================

Installs Docker from official Docker binaries archive (no PPA or apt repository). For managing Docker daemon systemd is used. Should work with basically every Linux OS using `systemd`. This Ansible playbook is used in [Kubernetes the not so hard way with Ansible - Control plane](https://www.tauceti.blog/posts/kubernetes-the-not-so-hard-way-with-ansible-control-plane/) and [Kubernetes the not so hard way with Ansible - Worker](https://www.tauceti.blog/posts/kubernetes-the-not-so-hard-way-with-ansible-worker-2020/).

Versions
--------

I tag every release and try to stay with [semantic versioning](http://semver.org). If you want to use the role I recommend to checkout the latest tag. The master branch is basically development while the tags mark stable releases. But in general I try to keep master in good shape too. A tag `8.0.0+20.10.11` means this is release `8.0.0` of this role and it's meant to be used with Docker version `20.10.11`. If the role itself changes `X.Y.Z` before `+` will increase. If the Docker version changes `XX.YY.ZZ` after `+` will increase. This allows to tag bugfixes and new major versions of the role while it's still developed for a specific Docker release.

Requirements
------------

A recent kernel should be used (something like >= 4.4.x). It makes sense to use a recent kernel for Docker in general. I recommend to use >= 4.8.x if possible. Ubuntu 18.04 and up have already a descent kernel by default. Verify that you have `overlay2` filesystem available if you want to use it instead of e.g. `aufs` which is recommended in production (should be the case for kernel >= 4.4.x). Meanwhile `aufs` is deprecated anyways. But you can change the storage driver in the `dockerd_settings_user` (see below for more information) if you like.

**NOTE**: The default variables of the role variables are configured to work with Kubernetes (and `Flannel` overlay network or `Cilium`). If you want to use this role without Kubernetes you may want to adjust a few settings (especially `iptables`, `ip-masq`, `bip` and `mtu` `dockerd_settings`). See comment for `dockerd_settings` for more information. I disabled most parts of `Docker` networking as it' not needed for `Kubernetes`.

Changelog
---------

see [Changelog](https://github.com/githubixx/ansible-role-docker/blob/master/CHANGELOG.md)

Role Variables
--------------

```
# Directory to store downloaded Docker archive and unarchived binary files.
docker_download_dir: "/opt/tmp"

# Docker version to download and use.
docker_version: "20.10.12"
docker_user: "docker"
docker_group: "docker"
docker_uid: 666
docker_gid: 666
# Directory to store Docker binaries. Should be in your search PATH!
docker_bin_dir: "/usr/local/bin"

# Settings for "dockerd" daemon. Will be provided as paramter to "dockerd" in
# systemd service file for Docker. This settings are used to work out of the
# box with Kubernetes, Flannel network overlay or Cilium. If you don't need this
# and just want to use "default" Docker networking see below (`dockerd_settings_user`
# variable):
dockerd_settings:
  "host": "unix:///run/docker.sock"
  "log-level": "error"
  "storage-driver": "overlay2"
  "iptables": "false"
  "ip-masq": "false"
  "bip": ""
  "mtu": "1472"

# If you need the default Docker settings define this variable in "group_vars/all.yml" e.g.:
# dockerd_settings_user:
# "host": "unix:///run/docker.sock"
# "log-level": "info"
# "storage-driver": "aufs"
# "iptables": "true"
# "ip-masq": "true"
# "bip": "172.17.0.0/16"
# "mtu": "1500"

# The directory from where to copy the Docker CA certificates. By default this
# will expand to user's LOCAL $HOME (the user that run's "ansible-playbook ..."
# plus "/docker-ca-certificates". That means if the user's $HOME directory is e.g.
# "/home/da_user" then "docker_ca_certificates_src_dir" will have a value of
# "/home/da_user/docker-ca-certificates".
docker_ca_certificates_src_dir: "{{ '~/docker-ca-certificates' | expanduser }}"

# The directory where the program "update-ca-certificates" searches for CA certificate
# files (besides other locations).
docker_ca_certificates_dst_dir: "/usr/local/share/ca-certificates"
```

Variables with no defaults:

```
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

These settings for `dockerd` daemon defined in `dockerd_settings` can be overridden by defining a variable called `dockerd_settings_user`. You can also add additional settings by using this variable. E.g. if you add the following variable and it's settings to `group_vars/all.yml` (or where ever it fit's best for you) `dockerd` will run with the default settings and overrides the default settings of this role (see above):

```
dockerd_settings_user:
  "host": "unix:///run/docker.sock"
  "log-level": "info"
  "storage-driver": "aufs"
  "iptables": "true"
  "ip-masq": "true"
  "bip": "172.17.0.0/16"
  "mtu": "1500"
```

Of course you can add more settings. Just add ;-)

Upgrading Docker
---------------

If you want upgrade Docker update `docker_version` variable accordingly. Afterwards if you run `ansible-playbook` and supply the argument `--extra-vars="upgrade_docker=true"` the playbook will download the specified Docker version and installs the binaries. This will cause systemd to restart `docker.service`. To avoid restarting all Docker daemons on all of your hosts at once consider using `--limit` parameter or reduce parallel Ansible tasks with `--forks`.

Example Playbook
----------------

```
- hosts: docker_hosts
  roles:
    - githubixx.docker
```

Testing
-------

This role has a small test setup that is created using [Molecule](https://github.com/ansible-community/molecule), libvirt (vagrant-libvirt) and QEMU/KVM. Please see my blog post [Testing Ansible roles with Molecule, libvirt (vagrant-libvirt) and QEMU/KVM](https://www.tauceti.blog/posts/testing-ansible-roles-with-molecule-libvirt-vagrant-qemu-kvm/) how to setup. The test configuration is [here](https://github.com/githubixx/ansible-role-docker/tree/master/molecule/default).

Afterwards molecule can be executed:

```bash
molecule converge
```

This will setup a few virtual machines (VM) with different supported Linux operating systems and installs `docker` role.

To clean up run

```bash
molecule destroy
```

License
-------

GNU GENERAL PUBLIC LICENSE Version 3

Author Information
------------------

[http://www.tauceti.blog](http://www.tauceti.blog)
