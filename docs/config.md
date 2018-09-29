# Variables
## config
>>NOTE: Unknown keys are ignored.
>>NOTE: The same keys of *_linkagg are adopted.

eos_mlag_linkaggs:
  - group: "101"
    mlag: "101"
    mode: "active"
    members:
      - "Ethernet3"

# Requirements
ansible_network_os: eos
ansible_network_provider: ansible-network.arista_eos

# Dependencies
ansible-network.config_manager
ansible-network.arista_eos