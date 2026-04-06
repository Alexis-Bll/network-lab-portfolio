# Lab Documentation

This folder contains structured documentation for each phase of the enterprise network lab.

The lab has been built in a phased approach to reflect how a real-world enterprise network would be deployed, from initial device configuration through to routing, security, and validation.

Each phase demonstrates both configuration and the reasoning behind the design decisions.

---

## Structure

The lab is divided into multiple phases, following a logical enterprise deployment workflow:

### 00 - Base Configuration
Initial device setup including:
- Hostnames
- Secure access (SSH)
- Local authentication
- Device hardening

---

### 01 - Core Layer Setup
Core network implementation including:
- VLAN creation
- Inter-VLAN routing (SVIs)
- HSRP for gateway redundancy
- EtherChannel for link aggregation
- STP tuning for optimal Layer 2 forwarding

---

### 02 - Access Layer
Access layer configuration including:
- VLAN assignment for end devices
- Trunk links to the core
- PortFast and BPDU Guard
- Layer 2 security (DAI, DHCP Snooping, Port Security)

---

### 03 - Routing & WAN (OSPF)
Enterprise routing configuration including:
- OSPF deployment across all Layer 3 devices
- Multi-site connectivity (HQ ↔ Branches via ISP)
- Private WAN/MPLS-style routing design
- Passive interfaces and route optimisation

---

### 04 - Branch Integration
Branch network deployment including:
- Branch switch configuration
- VLAN segmentation per site
- Integration into the wider enterprise network via branch routers

---

### 05 - Access Control & Security
Traffic filtering and policy enforcement including:
- Extended ACL implementation
- Inter-department traffic restriction
- Maintaining access to shared HQ resources
- Verification using hit counters and connectivity tests

---

## Documentation Approach

Each lab file includes:

- Objective of the phase
- Configuration steps
- Relevant commands
- Verification procedures
- Key learning points

This ensures the lab demonstrates not just configuration, but a clear understanding of **why each technology is used**.

---

## Configurations

Full device configurations can be found in the `configs/` directory.

---

## Notes

This lab is designed to reflect enterprise networking best practices at a CCNA level, with elements extending slightly beyond to demonstrate deeper understanding.

A follow-up lab will be developed with an updated design to demonstrate additional features such as:
- NAT and internet edge connectivity
- More advanced security controls
- Expanded routing scenarios
