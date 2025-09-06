<picture>
  <img src="../../assets/images/banner_sealand.png" alt="Banner" style="width: 100%;">
</picture>

# Sealand 
## Sealand Principality Project – Final Project for Intermediate Vocational Training in Microcomputer Systems and Networks, I.E.S. Galileo
### Description

This is my final networking project for the 1st SMR course, consisting of a Packet Tracer file and an explanatory PDF. The project simulates a multi-VLAN network for the Principality of Sealand, including routing, switching, DHCP, DNS, and web server configuration.

### Links to project

* [Packet Tracer archive](/networking/01_Sealand_es/packet_tracer/01_Sealand.pkt)
* [Topology](/networking/01_Sealand_es/docs/01_sealand.png)
* [Project](/networking/01_Sealand_es/config/01_Sealand_es.md)
#### 1. Key Components:

* Routers: Cisco 2911 with serial modules.
* Switches: Cisco Catalyst 2960.
* VLANs: VLAN104 and VLAN204.
* Trunking: VLANs 104 & 204 transported over trunk ports, native VLAN changed to 99 for security.
* DHCP: Pools configured for VLANs, excluding gateway addresses.
* Routing: Static routes (ip route) configured across all routers

##### 1.1. Servers:

* DNS server (172.5.4.8) with entries for 2 web pages.

* Two web servers (172.5.4.10 and 10.3.4.2)

#### Configuration Highlights:

* Switch port configuration for access and trunk modes

* Router subinterfaces for VLAN tagging (dot1Q)

* Serial interface setup with clock rate on DCE routers

* Network segmentation and inter-VLAN routing

* End-to-end connectivity verification through DNS and HTTP services

This project demonstrates practical network design, VLAN implementation, routing, and server configuration using Cisco Packet Tracer.
