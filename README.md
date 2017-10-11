ansible-role-docker
===================

Installs Docker from official Docker binaries archive. For managing Docker daemon systemd is used. Targeting mainly Ubuntu 16.04 but should work with other Linux OS using systemd. This Ansible playbook is used in [Kubernetes the not so hard way with Ansible (at Scaleway) - Part 6 - Control plane](https://www.tauceti.blog/post/kubernetes-the-not-so-hard-way-with-ansible-at-scaleway-part-6/) and [Kubernetes the not so hard way with Ansible (at Scaleway) - Part 7 - Worker](https://www.tauceti.blog/post/kubernetes-the-not-so-hard-way-with-ansible-at-scaleway-part-7/).

Versions
--------

I tag every release and try to stay with [semantic versioning](http://semver.org). If you want to use the role I recommend to checkout the latest tag. The master branch is basically development while the tags mark stable releases. But in general I try to keep master in good shape too. A tag `r1.0.0_v17.03.2-ce` means this is release 1.0.0 of this role and it's meant to be used with Docker version 17.03.2-ce. If the role itself changes `rX.Y.Z` will increase. If the Docker version changes `vAA.BB.C-xy` will increase. This allows to tag bugfixes and new major versions of the role while it's still developed for a specific Docker release.

Requirements
------------

Since this role is targeting Ubuntu 16.04 a recent kernel should be used (at time of writing it was kernel 4.10.x on my VPS instance). It makes sense to use a recent kernel for Docker in general. Ubuntu 16.04 additionally provides kernel 4.4.x and 4.8.x. I recommend to use >= 4.8.x if possible. Verify that you have `overlayfs` filesystem available (execute `cat /proc/filesystems | grep overlay`. If you see an output you should be fine) on your controller and worker instances. In my case `overlayfs` is compiled into the kernel. If it's not compiled in you can normally load it via `modprobe -v overlay` (`-v` gives us a little bit more information). We'll configure Docker to use overlayfs by default because it's one of the best choises (Docker 1.13.x started to use overlayfs by default if available). But you can change the storage driver via the `docker_storage_driver` variable if you like.

The default variables of the role variables are configured to work with Kubernetes and Flannel overlay network. If you want to use this role without Kubernetes you may want to adjust a few settings (especially `docker_iptables`, `docker_ip-masq`, `docker_bip` and `docker_mtu`). We disable most parts of Docker networking as we leave this up to Flannel.

Role Variables
--------------

```
docker_download_dir: /opt/docker

docker_version: 17.03.2-ce
docker_user: docker
docker_group: docker
docker_uid: 666
docker_gid: 666
docker_bin_dir: /usr/local/bin
docker_storage_driver: overlay
docker_log_level: error
docker_iptables: false
docker_ip_masq: false
docker_bip: ""
docker_mtu: 1472
```

Example Playbook
----------------

```
- hosts: docker_hosts
  roles:
    - githubixx.docker
```

License
-------

GNU GENERAL PUBLIC LICENSE Version 3

Author Information
------------------

[http://www.tauceti.blog](http://www.tauceti.blog)
