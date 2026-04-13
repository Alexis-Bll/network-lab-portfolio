# Device Configurations

This folder contains the final, cleaned running configurations for all devices within the enterprise network lab.

Each configuration file represents a fully implemented and validated device, structured to reflect real-world enterprise deployment standards.

---

## Project Status

### ✅ Completed

The enterprise network lab has been fully configured, tested, and documented.

This includes:

* Full device configurations across all routers and switches
* End-to-end connectivity validation
* OSPF route propagation across all sites
* HSRP failover and redundancy testing
* ACL enforcement between departments
* SSH management access across all devices

All configurations in this folder are final and reflect the working state of the lab.

---

## Configuration Scope

This folder includes configurations for:

### WAN / Edge Devices

* ISP router
* HQ-R1 / HQ-R2
* BR1 / BR2

### Core Layer

* HQ-CSW1 / HQ-CSW2

### Access Layer

* HQ-ASW1 / HQ-ASW2 (Admin VLAN)
* HQ-SSW1 / HQ-SSW2 (Server VLAN)
* BR-SW1 / BR-SW2 (Branch switches)

---

## Technologies Implemented

The configurations demonstrate:

* VLAN segmentation and inter-VLAN routing (SVIs)
* OSPF dynamic routing across a multi-site network
* HSRP for gateway redundancy and load sharing
* EtherChannel (LACP) for link aggregation
* Rapid PVST+ with STP tuning (root bridge placement)
* Access Control Lists (ACLs) for traffic filtering
* DHCP Snooping and Dynamic ARP Inspection (DAI)
* Port Security and STP protections (PortFast, BPDU Guard)
* Secure remote access using SSH

---

## Engineering Approach

Configurations have been intentionally cleaned and structured to:

* Remove unnecessary default or auto-generated Cisco output
* Highlight only relevant and meaningful configuration
* Maintain consistency across all devices
* Improve readability for documentation and review

This reflects real-world practices where configurations are reviewed, standardised, and documented before deployment or handover.

---

## V2 Enhancements

The configurations in this folder represent the completed V1 implementation of the lab.

In V2, the existing design is extended with additional functionality while maintaining the same core architecture.

Key enhancements include:

- NAT (PAT) configured at the HQ edge (HQ-R1) for internet access  
- Introduction of centralised services (DHCP, DNS, Active Directory via DC1)  
- Integration of an application server within the server VLAN  
- Minor updates to router configurations to support external connectivity  

These changes build upon the existing configurations rather than replacing them, reflecting how real enterprise networks evolve incrementally.

---
