# About

This function/method perform configuration of MLAG interfaces on EOS devices, adopting the basic datamodel (`mlag_config`) specified by `ansible-network.mlag`.

The datamodel specification has an extension to accomodate platform-specific specifications in `eos_mlag_setup.extension`. 

## Function Parameters (Specs)

For additional details about variables supported by this role and theirs respective default values [config_spec](https://github.com/victorock/ansible-role-eos-mlag/blob/devel/meta/config_spec.yaml)


## How to configure MLAG interfaces adopting a data-driven approach:

With a data-driven approach, all the specifications are defined at inventory-level either in `group_vars` or `host_vars`.

Roles called by playbooks will consume the data coming from the inventory based on role's definitions/specifications and implementations.


## Specify Scope at Inventory

### Scope: Platform

> /inventory/group_vars/eos/ansible.yaml

```
ansible_network_os: eos
ansible_network_provider: ansible-network.arista_eos
```

### Scope: Site

> /inventory/group_vars/site-1/site.yaml

```
site_id: "1"
site_vlans:
  - "10"
  - "100-1000"
```


### Scope: Domain

> /inventory/group_vars/domain-1/domain.yaml

```
domain_id: "1"
domain_vlans:
  - "10"
  - "100-200"

domain_interlinks:
  - "Ethernet1"
  - "Ethernet2"

domain_uplinks:
  - "Ethernet47"
  - "Ethernet48"

domain_downlinks:
  - "Ethernet3-Ethernet46"
```

> /inventory/group_vars/domain-1/vlan.yaml

```
vlan_config:
  "100-200":
    name: "100-200"
```

> /inventory/group_vars/domain-1/interface.yaml

```
interface_config:
  "Ethernet3":
    name: "Ethernet3"
    enabled: "true"
    speed: "auto"
```

> /inventory/group_vars/domain-1/mlag.yaml

```
mlag_config:
  "Ethernet3":
    name: "Ethernet3"
    group:
      id: 103
      mode: active
    mlag: 103
```

> /inventory/group_vars/domain-1/switchport.yaml

```
switchport_config:
  "Port-Channel103":
    name: "Port-Channel103"
    mode: "trunk"
    allowed_vlans:
      - 100
      - 101
```

### Scope: Device

> /inventory/host_vars/veos-1/device.yaml

```
device_peer: "veos-2"
device_id: "1"
```

> /inventory/host_vars/veos-2/device.yaml

```
device_peer: "veos-1"
device_id: "2"
```

### Scope: Feature
> /inventory/group_vars/feature-mlag/setup.yaml

```
mlag_setup:
  site: "{{ site_id }}"
  domain: "{{ domain_id }}"
  interlinks: "{{ domain_interlinks }}"
  vlans: "{{ domain_vlans }}"
  peer: "{{ device_peer }}"
  device: "{{ device_id }}"
```

## Consume at Playbook

> /playbooks/main.yaml

```
- import_playbook: "site-1/main.yaml"
- import_playbook: "site-2/main.yaml"
- import_playbook: "site-N/main.yaml"
```

> /playbooks/site-1/main.yaml

```
- import_playbook: "domain-1/main.yaml"
- import_playbook: "domain-2/main.yaml"
- import_playbook: "domain-N/main.yaml"
```

> /playbooks/site-1/domain-1/main.yaml

```
- import_playbook: "feature-mlag/main.yaml"
- import_playbook: "feature-switchport/main.yaml"
- import_playbook: "feature-interface/main.yaml"
```

> /playbooks/site-1/domain-1/feature-mlag/main.yaml

```
- import_playbook: "setup.yaml"
- import_playbook: "config.yaml"
```

> /playbooks/site1/domain-1/feature-mlag/config.yaml

```
- name: "MLAG Setup: domain-1"
  hosts: "site-1:&domain-1:&feature-mlag"
  connection: network_cli
  gather_facts: no
  tasks:
    - name: "Config MLAG"
      include_role:
        name: "ansible-network.eos_mlag"
        tasks_from: "config.yaml"
```

## Requirements

The following variables must be set for `ansible-network.config_manager`:

```
ansible_network_os: eos
ansible_network_provider: ansible-network.arista_eos
```

# Dependencies

This role requires the following roles:

```
ansible-network.config_manager
ansible-network.arista_eos
```