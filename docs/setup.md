# Variables
## setup
eos_mlag_neighbor_host: "vEOS-2"
eos_mlag_vlan_id: "4094"
eos_mlag_domain_id: "1"
eos_mlag_site_id: "1"
eos_mlag_device_id: "1"
eos_mlag_link_id: "1"
eos_mlag_vlans:
  - "all"
eos_mlag_links: 
  - "Ethernet1"
eos_mlag_heartbeat: "10000"
eos_mlag_reload_delay: "150"

# Requirements
ansible_network_os: eos
ansible_network_provider: ansible-network.arista_eos

# Dependencies
ansible-network.config_manager
ansible-network.arista_eos