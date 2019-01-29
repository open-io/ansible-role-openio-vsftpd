[![Build Status](https://travis-ci.org/open-io/ansible-role-openio-vsftpd.svg?branch=master)](https://travis-ci.org/open-io/ansible-role-openio-vsftpd)
# Ansible role `vsftpd`

Ansible role that installs and configures a FTP server using `vsftpd`

## Requirements

- Ansible 2.4+

## Role Variables

> Most boolean options in the default configuration are set to provide maximum compatibility with FTP clients. Changing flags may result in abnormal behavior on client side.


| `openio_vsftpd_allow_writeable_chroot`      | `true`                    | Descrtiption                                        |
| ------------------------------------------- | ------------------------- | --------------------------------------------------- |
| `openio_vsftpd_anon_enable`                 | `false`                   | Enable anonymous uploads                            |
| `openio_vsftpd_ascii_mangling_enable`       | `true`                    | Enable ASCII mangling                               |
| `openio_vsftpd_async_abor_enable`           | `true`                    | Enable async abort behavior                         |
| `openio_vsftpd_authorized_group`            | `ftpusers`                | Group of all FTP users                              |
| `openio_vsftpd_banner`                      | `"FTP Server"`            | FTP server banner                                   |
| `openio_vsftpd_bind_address`                | ``                        | FTP bind address                                    |
| `openio_vsftpd_bind_interface`              | ``                        | FTP bind interface                                  |
| `openio_vsftpd_bind_port`                   | `21`                      | FTP bind port                                       |
| `openio_vsftpd_chroot_local_user`           | `true`                    | Chroot users                                        |
| `openio_vsftpd_connect_from_port_20_enable` | `true`                    | Accept connections from port 20                     |
| `openio_vsftpd_connection_timeout`          | `120`                     | Connection timeout                                  |
| `openio_vsftpd_dirmessage_enable`           | `false`                   | Enable directory messages                           |
| `openio_vsftpd_idle_timeout`                | `120`                     | Idle timeout                                        |
| `openio_vsftpd_local_enable`                | `true`                    | Enable local userss                                 |
| `openio_vsftpd_local_umask`                 | `"022"`                   | File creation umask for local users                 |
| `openio_vsftpd_ls_recurse_enable`           | `true`                    | Enable recursive ls                                 |
| `openio_vsftpd_nopriv_user`                 | `ftpsecure`               | Default unprivileged user                           |
| `openio_vsftpd_pam_service_name`            | `vsftpd`                  | PAM service name                                    |
| `openio_vsftpd_passive_mode`                | `[dict]`                  | Passive mode configuration                          |
| `openio_vsftpd_provision_only`              | `false`                   | Only provision without restarting                   |
| `openio_vsftpd_ssl_allow_anon`              | `true`                    | Allow anonymous SSL (Anon must be enabled)          |
| `openio_vsftpd_ssl_cert_dir`                | `/etc/ssl/private`        | Path to certificate directory                       |
| `openio_vsftpd_ssl_cert_file`               | `vsftpd.pem`              | Path to certificate file                            |
| `openio_vsftpd_ssl_cert_generate`           | `true`                    | Generate autosigned certificate if it doesn't exist |
| `openio_vsftpd_ssl_ciphers`                 | `HIGH`                    | SSL cipher security level                           |
| `openio_vsftpd_ssl_enable`                  | `true`                    | Enable SSL                                          |
| `openio_vsftpd_ssl_force_local_data`        | `true`                    | Force data to use SSL                               |
| `openio_vsftpd_ssl_force_local_logins`      | `true`                    | Force logins to use SSL                             |
| `openio_vsftpd_ssl_proto`                   | `tlsv1`                   | SSL Protocol (TLSV1/SSLV2/SSLV3)                    |
| `openio_vsftpd_ssl_require_reuse`           | `true`                    | SSL require reusable sessions                       |
| `openio_vsftpd_syslog_enable`               | `true`                    | Log to syslog                                       |
| `openio_vsftpd_systemd_managed`             | `true`                    | VSFTPD server is managed by systemd                 |
| `openio_vsftpd_tcp_wrappers_enable`         | `true`                    | Enable TCP wrappers                                 |
| `openio_vsftpd_write_enable`                | `true`                    | Enable FTP write                                    |

> Certificate must be comprised of key + crt in the same .pem file, much like the output of below command.
> `openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/ssl/private/vsftpd.pem -out /etc/ssl/private/vsftpd.pem`

## Dependencies

- [ansible-role-openio-users](https://github.com/open-io/ansible-role-openio-users)

## Example Playbook

```yaml
- hosts: all
  become: true
  pre_tasks:
      - file:
          name: /mnt/ftp
          state: directory
          owner: root
          group: root
          mode: 0755
  roles:
    - role: users
      openio_users_add:
        - username: ftpsecure
          name: Default ftp user
          group: ftpusers
          home_create: false
          system: true
          shell: /sbin/nologin
          groups: []
        - username: testuser
          name: FTP Test user
          group: ftpusers
          home_create: true
          system: true
          shell: /sbin/nologin
          groups: []
          password: "{{ '1234' | password_hash('sha512') }}"
          update_password: on_create
      openio_users_home: /mnt/ftp
      openio_users_groups:
        - groupname: ftpusers
    - role: vsftpd
```

```ini
[all]
node1 ansible_host=192.168.1.173
```

## Contributing

Issues, feature requests, ideas are appreciated and can be posted in the Issues section.

Pull requests are also very welcome.
The best way to submit a PR is by first creating a fork of this Github project, then creating a topic branch for the suggested change and pushing that branch to your own fork.
Github can then easily create a PR based on that branch.

## License

GNU AFFERO GENERAL PUBLIC LICENSE, Version 3

## Contributors

- [Vladimir DOMBROVSKI](https://github.com/vdombrovski) (maintainer)
- [Cedric DELGEHIER](https://github.com/cdelgehier) (maintainer)
- [Romain ACCIARI](https://github.com/racciari) (maintainer)
- [Vincent LEGOLL](https://github.com/vincent-legoll) (maintainer)
