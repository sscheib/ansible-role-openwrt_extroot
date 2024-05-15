<!-- markdownlint-disable MD013 MD041 -->
[![ansible-lint](https://github.com/sscheib/ansible-role-openwrt_extroot/actions/workflows/ansible-lint.yml/badge.svg)](https://github.com/sscheib/ansible-role-openwrt_extroot/actions/workflows/ansible-lint.yml) [![Publish to Ansible Galaxy](https://github.com/sscheib/ansible-role-openwrt_extroot/actions/workflows/release.yml/badge.svg)](https://github.com/sscheib/ansible-role-openwrt_extroot/actions/workflows/release.yml) [![markdown link check](https://github.com/sscheib/ansible-role-openwrt_extroot/actions/workflows/markdown-link-check.yml/badge.svg)](https://github.com/sscheib/ansible-role-openwrt_extroot/actions/workflows/markdown-link-check.yml) [![markdownlint](https://github.com/sscheib/ansible-role-openwrt_extroot/actions/workflows/markdownlint.yml/badge.svg)](https://github.com/sscheib/ansible-role-openwrt_extroot/actions/workflows/markdownlint.yml) [![pyspelling](https://github.com/sscheib/ansible-role-openwrt_extroot/actions/workflows/pyspelling.yml/badge.svg)](https://github.com/sscheib/ansible-role-openwrt_extroot/actions/workflows/pyspelling.yml)

[![pre-commit](https://img.shields.io/badge/pre--commit-enabled-brightgreen?logo=pre-commit&logoColor=white)](https://github.com/pre-commit/pre-commit) [![Conventional Commits](https://img.shields.io/badge/Conventional%20Commits-1.0.0-%23FE5196?logo=conventionalcommits&logoColor=white)](https://conventionalcommits.org) [![License: GPL v2](https://img.shields.io/badge/License-GPL_v2-blue.svg)](https://www.gnu.org/licenses/old-licenses/gpl-2.0.en.html)
<!-- markdownlint-disable MD013 MD041 -->

## openwrt_extroot

This role deploys an [`Extroot` configuration](https://openwrt.org/docs/guide-user/additional-software/extroot_configuration) on `OpenWrt` devices.

**Please note**:

- This role requires Python to be installed on your `OpenWrt` device. If you are looking for a role to bootstrap Python on your `OpenWrt` device, the role
  [`openwrt_bootstrap`](https://github.com/sscheib/ansible-role-openwrt_bootstrap) might be of interest to you.
- Further, this role only supports creating one single primary partition on the USB drive to be used as destination for the `Extroot`

## Role Variables

| variable                                     | default                      | required | description                                                                    |
| :---------------------------------           | :--------------------------- | :------- | :----------------------------------------------------------------------------- |
| `ext_usb_device`                             | unset                        | true     | Path to an USB device to use for the `Extroot` configuration (e.g. `/dev/sda`) |
| `ext_usb_device_part`                        | `{{ ext_usb_device }}1`      | false    | Path to a partition to create on the USB device (e.g. `/dev/sda1`)             |
| `ext_create_flash_mount`                     | `true`                       | false    | Whether to create a mount point for the flash configuration                    |
| `ext_flash_mount_path`                       | `/rwm`                       | false    | Path to the flash mount point to create                                        |
| `ext_flash_mount_owner`                      | `root`                       | false    | Owner of `ext_flash_mount_path`                                                |
| `ext_flash_mount_group`                      | `root`                       | false    | Group of `ext_flash_mount_path`                                                |
| `ext_flash_mount_mode`                       | `0644`                       | false    | Mode of `ext_flash_mount_path`                                                 |
| `ext_flash_mount_uci_name`                   | `rwm`                        | false    | `UCI`[^uci] configuration section name                                         |
| `ext_filesystem_type`                        | `ext4`                       | false    | Filesystem type to create on `ext_usb_device_part`[^file_system]               |
| `ext_overlay_filesystem_path`                | `/overlay`                   | false    | Path where the overlay file system is mounted                                  |
| `ext_tmp_root_mount_path`                    | `/tmp/cproot`                | false    | Path where `ext_overlay_filesystem_path` gets bind mounted temporarily         |
| `ext_tmp_root_mount_owner`                   | `root`                       | false    | Owner of `ext_tmp_root_mount_path`                                             |
| `ext_tmp_root_mount_group`                   | `root`                       | false    | Group of `ext_tmp_root_mount_path`                                             |
| `ext_tmp_root_mount_mode`                    | `0700`                       | false    | Mode of `ext_tmp_root_mount_path`                                              |
| `ext_tmp_mount_path`                         | `/mnt`                       | false    | Path where the USB drive gets mounted temporarily to copy data onto it         |
| `ext_tmp_mount_owner`                        | `root`                       | false    | Owner of `ext_tmp_mount_path`                                                  |
| `ext_tmp_mount_group`                        | `root`                       | false    | Group of `ext_tmp_mount_path`                                                  |
| `ext_tmp_mount_mode`                         | `0700`                       | false    | Mode of `ext_tmp_mount_path`                                                   |
| `ext_required_packages`                      | See `defaults/main.yml`      | false    | Packages which are required to setup the `Extroot`                             |
| `ext_quiet_assert`                           | `false`                      | false    | Whether to quiet assert statements                                             |
| `ext_skip_assert`                            | `false`                      | false    | Whether to skip the initial assert statements. **Use with caution!**           |

## Requirements

This role makes use of the `parted` module which is part of the collection [`community.general`](https://github.com/ansible-collections/community.general), which is
specified via `collections/requirements.yml`.

## Dependencies

None

## Example Playbook

```yaml
---
- name: 'Create Extroot configuration on OpenWrt devices'
  hosts: 'all'
  roles:
    - role: 'openwrt_extroot'
  vars:
    ext_usb_device: '/dev/sda'
    ext_usb_device_part: '/dev/sda1'
    ext_create_flash_mount: true
    ext_flash_mount_path: '/rwm'
    ext_flash_mount_owner: 'root'
    ext_flash_mount_group: 'root'
    ext_flash_mount_mode: '0644'
    ext_flash_mount_uci_name: 'rwm'
    ext_filesystem_type: 'ext4'
    ext_overlay_filesystem_path: '/overlay'
    ext_tmp_root_mount_path: '/tmp/cproot'
    ext_tmp_root_mount_owner: 'root'
    ext_tmp_root_mount_group: 'root'
    ext_tmp_root_mount_mode: '0700'
    ext_tmp_mount_path: '/mnt'
    ext_tmp_mount_owner: 'root'
    ext_tmp_mount_group: 'root'
    ext_tmp_mount_mode: '0700'
    ext_quiet_assert: false
    ext_skip_assert: false
    ext_required_packages:
      - 'e2fsprogs'
      - 'blkid'
      - 'blockdev'
      - 'tune2fs'
      - 'block-mount'
      - 'kmod-fs-ext4'
      - 'wipefs'
      - 'rsync'
      - 'parted'
      - 'kmod-usb-storage'
...
```

## Contributing

First off, thanks for taking the time to contribute! ❤️

All types of contributions are encouraged and valued.
Please See the [`CONTRIBUTING.md`](CONTRIBUTING.md) for different ways to help and details about how this project handles them.

## License

[`GPL-2.0-or-later`](LICENSE)

[^uci]: [UCI explanation](https://openwrt.org/docs/techref/uci)
[^file_system]: If it differs from the default (`ext4`), you also need to adjust the required packages to include the respective kernel modules required
