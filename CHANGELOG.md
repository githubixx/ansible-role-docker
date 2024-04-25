# Changelog

## 11.2.0+26.1.0

- update Docker to `v26.1.0`
- update Docker compose to `v2.27.0`
- Molecule: use `alvistack` instead of `generic` Vagrant boxes for Ubuntu

## 11.1.0+25.0.0

- add support for installing `docker-compose`

## 11.0.0+25.0.0

- **BREAKING**: remove `Debian 10` and `Ubuntu 18` support
- add `Debian 12` support
- update Docker to `v25.0.0`
- update Molecule tests
- update Github actions workflow

## 10.0.0+23.0.4

- update Docker to `v23.0.4`
- fix Molecule prepare step for Archlinux

## 9.0.0+20.10.22

- **BREAKING:** `dockerd_settings` now contains the default settings for `dockerd`. Previous versions had optimized settings for Kubernetes. But since Docker is no longer relevant for Kubernetes it makes sense to just use the default settings. Settings before this version:

```yaml
dockerd_settings:
  "host": "unix:///run/docker.sock"
  "log-level": "error"
  "storage-driver": "overlay2"
  "iptables": "false"
  "ip-masq": "false"
  "bip": ""
  "mtu": "1472"
```

- update Docker to `v20.10.22`
- add support for Ubuntu `22.04` (Jammy Jellyfish)
- add `iptables`/`nftables` handling
- add `.gitignore`
- add `.yamllint`
- add Molecule `verify` step
- increase memory for Molecule test instances
- add Github release action to push new release to Ansible Galaxy

## 8.2.0+20.10.17

- update Docker to `v20.10.17`

## 8.1.0+20.10.12

- update Docker to `v20.10.12`
- add missing file `containerd-shim-runc-v2`

## 8.0.1+20.10.11

- update README
- make `meta/main.yml` valid YAML file

## 8.0.0+20.10.11

- update Docker to `v20.10.11`
- add support for Debain 10 (Buster) + 11 (Bullseye)
- add support for Archlinux
- `aufs` storage driver is deprecated -> use `overlay2` by default
- add Molecule tests

## 7.0.1+18.09.9

- added Ubuntu 20.04 (Focal Fossa) as supported platform

## 7.0.0+18.09.9

- Update Docker to `v18.09.9`
- value for `ListenStream` changed to `/run/docker.sock` (old path is deprecated)
- change `--storage-driver` to `overlay2` (see [Legacy overlay storage driver](https://docs-stage.docker.com/engine/deprecated/#legacy-overlay-storage-driver))

## no change

- Deleted old tags not supported by Ansible Galaxy:

```bash
r1.0.0_v17.03.2-ce
r2.0.0_v17.03.2-ce
r3.0.0_v17.03.2-ce
r3.1.0_v17.03.2-ce
v1.0.0_r1.12.6
```

## 6.0.0+18.09.6

- Update Docker to `v18.09.6`

## 5.1.0+18.06.1

- fix link to CHANGELOG
- update README / provide info about restoring default dockerd settings

## 5.0.0+18.06.1

- restart Docker daemon if docker binaries changes
- by specifying `--extra-vars="upgrade_docker=true"` to `ansible-playbook` download/unzip of new Docker archive is forced

## 4.0.0+18.06.1

- use correct semantic versioning as described in [semantic versioning](https://semver.org). Needed for Ansible Galaxy importer as it now insists on using semantic versioning.
- moved changelog entries to separate file
- make Ansible linter happy
- use systemd module instead of systemctl command for handlers
- increase min. Ansible version from 2.2 to 2.4
- no major changes but decided to start a new major release as versioning scheme changed quite heavily

## r3.1.1_v18.06.1-ce

- Update Docker to `v18.06.1-ce`

## r3.1.0_v17.03.2-ce

- introduce `docker_ca_certificates_src_dir`, `docker_ca_certificates_dst_dir` and `docker_ca_certificates` variables

## r3.0.0_v17.03.2-ce

- works with Ubuntu 18.04
- update README

## r2.0.0_v17.03.2-ce*

- major refactoring
- introduce flexible parameter settings for dockerd daemon via `dockerd_settings` and `dockerd_settings_user`

## r1.0.0_v17.03.2-ce

- initial release
