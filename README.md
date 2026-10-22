# Phansible Role: PHP

[![CI](https://github.com/phansible/role-php/actions/workflows/ci.yml/badge.svg)](https://github.com/phansible/role-php/actions/workflows/ci.yml)

An Ansible Role that installs [PHP](https://www.php.net/) and PHP-FPM from the [packages.sury.org](https://packages.sury.org/php/) repository on Ubuntu and Debian, created with [Phansible](http://phansible.com/) in mind.

## Requirements

- Ubuntu 22.04, 24.04, 26.04 or Debian 12, 13 (x86_64)
- Ansible >= 2.15
- Root access or `become` on the target. The configuration and PECL tasks do not set `become` themselves, so run the play with `become: yes` or as root.
- PHP 8.2, 8.3, 8.4 and 8.5 are tested in CI

## Role Variables

Defaults from `defaults/main.yml`:

| Variable | Default | Description |
| --- | --- | --- |
| `php_version` | `"8.4"` | PHP version to install; packages are named `php<version>-*` |
| `php_config_prefix` | `"/etc/php/{{ php_version }}"` | Directory holding the `cli`, `fpm` and `apache2` configuration |
| `php.packages` | `["cli", "intl"]` | Extension packages to install, without the `php<version>-` prefix |
| `server.timezone` | `"Europe/London"` | Written to `date.timezone` in every `php.ini` the role finds |

Optional, not set by default:

| Variable | Description |
| --- | --- |
| `php.peclpackages` | List of PECL extensions to build with `pecl install` (pulls in `php<version>-dev`), then enabled with `phpenmod` |

`php<version>` and `php<version>-fpm` are always installed. On the FPM `php.ini` the role also enables OPcache and turns off `expose_php`, and sets the `www` pool socket to `www-data:www-data` with mode `0660`. The CLI `php.ini` gets `opcache.enable_cli=1`. If `libapache2-mod-php` is already installed, its `php.ini` gets the timezone and OPcache settings too.

`php` and `server` are dictionaries, so an override replaces the whole default. `server` is shared with [role-server](https://github.com/phansible/role-server), which uses the same name.

## Dependencies

None.

## Example Playbook

```yaml
- hosts: all
  become: yes
  vars:
    php_version: "8.3"
    php:
      packages: [cli, intl, mbstring, xml, curl]
  roles:
    - role-php
```

## License

This role is released under the terms of the license specified in the repository or if not specified, under the [MIT license](https://raw.githubusercontent.com/phansible/role-php/master/LICENSE).

## Author Information

This role was created in 2015 by [The Phansible Team & Contributors](https://github.com/phansible/role-php/graphs/contributors), credits go also to [Jeff Geerling](http://jeffgeerling.com/), author of [Ansible for DevOps](http://ansiblefordevops.com/) and source of inspiration.
