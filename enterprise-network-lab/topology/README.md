# Enterprise Network Topologies

## Overview

This directory contains the network topology diagrams for the Enterprise Network Lab.

Two versions of the topology are included to demonstrate the progression of the lab from a private enterprise WAN design to a more complete enterprise network with public internet connectivity and centralised services.

Both versions are designed using CCNA-level networking concepts and reflect real-world enterprise architecture and design principles.

---

## What These Topologies Demonstrate

- Multi-site enterprise network design
- Hierarchical network architecture
- Private WAN connectivity between headquarters and branch offices
- Redundancy across the core and WAN edge
- Progression from internal enterprise design to a more realistic production-style environment

---

## Topology Versions

### V1 – Private Enterprise WAN Design

The first version of the topology focuses on the core enterprise network design and demonstrates:

- Headquarters and branch office connectivity across a private WAN
- Redundant HQ core architecture using dual Layer 3 switches
- Dynamic routing using OSPF
- Inter-VLAN routing at the core layer
- High availability through multiple physical and logical paths
- Layer 2 and Layer 3 security at the access layer and network edge

This version represents the foundational enterprise design and focuses on internal network connectivity without public internet access.

---

### V2 – Enhanced Enterprise Network Design

The second version builds upon the original design and introduces additional real-world components, including:

- Public internet connectivity from the HQ edge
- NAT at the internet-facing edge
- Centralised server infrastructure at HQ
- A domain controller for services such as DHCP, DNS, and Active Directory
- An application server to better reflect a business environment
- A clearer separation between private WAN connectivity and public-facing access

This version expands the scope of the lab to better simulate how enterprise networks operate in production environments.

---

## Design Progression

The purpose of maintaining both topology versions is to clearly demonstrate the evolution of the lab design.

### V1 focuses on:
- Private WAN/MPLS-style architecture
- Internal routing and redundancy
- Core enterprise networking principles

### V2 expands on this by introducing:
- Internet edge design
- NAT and external connectivity
- Centralised enterprise services
- Greater realism in network structure and traffic flow

This progression reflects a deliberate move from foundational CCNA-level enterprise design toward a broader and more realistic network implementation.

---

## Directory Structure

- `v1/` → Original enterprise topology
- `v2/` → Enhanced enterprise topology
- Root topology files may also be retained for reference during development and revision

---

## Scope

The topology folder is intended to visually support the rest of the project by showing how the lab design evolved across versions.

- **V1** represents the original private enterprise WAN implementation
- **V2** represents the current and more complete topology design

Further versions may be added in the future as additional technologies are explored.

---

## Summary

These topology diagrams document the design evolution of the Enterprise Network Lab, showing how multiple networking technologies and architectural decisions come together to provide:

- Scalable multi-site connectivity
- High availability through redundancy
- Clear separation of network roles

---
- Progression from private WAN design to enterprise internet edge integration

Together, they reflect both foundational CCNA-level knowledge and a growing understanding of practical enterprise network design.
