LNLS Ansible Role NTP
=======================

References: https://github.com/geerlingguy/ansible-role-ntp

This Ansible role configures NTP for Sirius Light Source control machines.

## Requirements

None.

## Role Variables

Available variables are listed below, along with default values (see `defaults/main.yml`):

    ntp_enabled: true

Whether to start the ntpd service and enable it at system boot. On many virtual machines that run inside a container (like OpenVZ or VirtualBox), it's recommended you don't run the NTP daemon, since the host itself should be set to synchronize time for all it's child VMs.

    ntp_timezone: Etc/UTC

Set the timezone for your server.

    ntp_manage_config: false

Set to true to allow this role to manage the NTP configuration file (`/etc/ntp.conf`).

    ntp_area: ''

Set the [NTP Pool Area](http://support.ntp.org/bin/view/Servers/NTPPoolServers) to use. Defaults to none, which uses the worldwide pool.

    ntp_servers:
      - "0{{ '.' + ntp_area if ntp_area else '' }}.pool.ntp.org iburst"
      - "1{{ '.' + ntp_area if ntp_area else '' }}.pool.ntp.org iburst"
      - "2{{ '.' + ntp_area if ntp_area else '' }}.pool.ntp.org iburst"
      - "3{{ '.' + ntp_area if ntp_area else '' }}.pool.ntp.org iburst"

Specify the NTP servers you'd like to use. Only takes effect if you allow this role to manage NTP's configuration, by setting `ntp_manage_config` to `True`.

    ntp_restrict:
      - "127.0.0.1"
      - "::1"

Restrict NTP access to these hosts; loopback only, by default.

## Dependencies

None.

## Example Playbook

```yaml
---
- hosts: all
  tasks:
  - import_role:
      name: '{{playbook_dir}}'
```

## Example Commmand

```bash
ansible-playbook -i host, -u user -k --ask-become-pass playbook.yml
```

## Tests

Tests are performed using Molecule. To run them with python virtualenv, issue:

```bash
    bash -c "\
        cd ../ && \
        virtualenv env --python python3 && \
        source env/bin/activate && \
        cd lnls-ans-role-ntp && \
        pip install molecule docker-py && \
        molecule test"
```

Optionally, you can specify dns servers to be used for both
provisioner create and run (docker in our case), by using
the following variables:


```bash
    export DNS_SERVER1="<DNS server 1>"
    export DNS_SERVER2="<DNS server 2>"
```

This can be used, for instance, in hosts that have non-default
DNS configurations that docker can't access, such as when
using local systemd-resolv DNS (e.g., 127.x.y.z) or when DNS
servers are set via DHCP with local non-routable IP addresses
(e.g., 10.x.y.z).

## Troubleshooting

If you use a host system with SELinux enabled you might get an error when using
Ansible like the following:

## License

MIT / BSD

## Author Information

This role was created in 2014 by [Jeff Geerling](https://www.jeffgeerling.com/), author of [Ansible for DevOps](https://www.ansiblefordevops.com/).
