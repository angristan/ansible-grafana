# Ansible role for Grafana

This role will setup [Grafana](https://github.com/grafana/grafana/) on Debian/Ubuntu based hosts.

## Role Variables

### Installation

The role will install Grafana from [the official APT repository](http://docs.grafana.org/installation/debian/).

### Configuration

- `grafana_ini_options`: set options in `grafana.ini` using the INI syntax via the Ansible INI module.

The default option is:

```yaml
grafana_ini_options:
  - {section: server, option: http_addr, value: 127.0.0.1}
```

See the [documentation](http://docs.grafana.org/installation/configuration/) for more information.

### Provisionning

There are ansible modules to add datasources and dashboards, but it is more practical to use file [provisionning](http://docs.grafana.org/administration/provisioning/).

You can use the `grafana_datasources` variable to add a datasource. The YAML is identical to the one described in the documentation, expect it's in a YAML sequence.

Example:

```yaml
grafana_datasources:
  - name: prometheus
    type: prometheus
    access: proxy
    url: http://localhost:9090
    isDefault: true
```

It's empty by default.

For dashboards, when `grafana_provision_dashboards` is set to `true`, the role will create `/etc/grafana/provisioning/dashboards/ansible.yml` that will contain the configuration needed to provision dashboards from the `/var/lib/grafana/dashboards` on the target host. The said folder will also be created.

Then, dashboard JSON files will be uploaded to this folder from the `grafana_dashboards_dir` folder, which defaults to `grafana/dashboards`.

For example in your project folder (where you have your playbook), you can put dashboards in `./files/grafana/dashboards/` and they will be imported by the role.

When `grafana_provisioning_synced` is set to `true`, the role will make sure that all the dashboards on the target host are also present on the deployer machine. Otherwise, they will be deleted.

When a datasource or a dashboard is added via provisionning, they are imported after a restart of Grafana, which the role handles automatically.

## Example playbook

```yaml
---

- hosts: myhost
  roles: grafana
  vars:
  grafana_ini_options:
    - {section: server, option: http_addr, value: 0.0.0.0}
    - {section: server, option: domain, value: grafana.domain.tld}
    - {section: server, option: root_url, value: https://grafana.domain.tld}

  grafana_datasources:
    - name: prometheus
      type: prometheus
      access: proxy
      url: http://localhost:9090
      isDefault: true

  grafana_provision_dashboards: true
  grafana_provisioning_synced: true
```

## License

MIT. See LICENSE for more details.

## Credit

This role is largely inspired by [cloudalchemy/ansible-grafana](https://github.com/cloudalchemy/ansible-grafana).

## Author Information

See my other Ansible roles at [angristan/ansible-roles](https://github.com/angristan/ansible-roles).
