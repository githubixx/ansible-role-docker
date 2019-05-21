Changelog
---------

**6.0.0+18.09.6**

- Update Docker to `v18.09.6`

**5.1.0+18.06.1**

- fix link to CHANGELOG
- update README / provide info about restoring default dockerd settings

**5.0.0+18.06.1**

- restart Docker daemon if docker binaries changes
- by specifing `--extra-vars="upgrade_docker=true"` to `ansible-playbook` download/unzip of new Docker archive is forced

**4.0.0+18.06.1**

- use correct semantic versioning as described in https://semver.org. Needed for Ansible Galaxy importer as it now insists on using semantic versioning.
- moved changelog entries to separate file
- make Ansible linter happy
- use systemd module instead of systemctl command for handlers
- increase min. Ansible version from 2.2 to 2.4
- no major changes but decided to start a new major release as versioning scheme changed quite heavily

**r3.1.1_v18.06.1-ce**

- Update Docker to `v18.06.1-ce`

**r3.1.0_v17.03.2-ce**

- introduce `docker_ca_certificates_src_dir`, `docker_ca_certificates_dst_dir` and `docker_ca_certificates` variables

**r3.0.0_v17.03.2-ce**

- works with Ubuntu 18.04
- update README

**r2.0.0_v17.03.2-ce**

- major refactoring
- introduce flexible parameter settings for dockerd daemon via `dockerd_settings` and `dockerd_settings_user`

**r1.0.0_v17.03.2-ce**

- initial release
