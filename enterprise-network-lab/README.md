# Enterprise Network Lab

## Overview

This lab simulates a small enterprise network based on technologies covered in the Cisco CCNA.

The design represents a realistic business environment with a headquarters (HQ), two branch offices, and an ISP. It demonstrates scalability, redundancy, and secure communication between sites using industry-relevant networking practices.

---

## Topology

<p align="center">
  <img src="./topology/ccna_lab.png" width="800">
</p>

### The topology includes:

- HQ with core and access layer switching
- Two branch offices (Sales and Engineering)
- Simulated ISP network (representing private MPLS/WAN connectivity)
- End devices and servers for testing

---

## Technologies Used

- VLANs (Network segmentation)
- Inter-VLAN Routing (SVIs on Layer 3 switches)
- OSPF (Dynamic routing across the enterprise)
- HSRP (Gateway redundancy at HQ)
- EtherChannel (Link aggregation between core switches)
- Access Control Lists (ACLs)
- DHCP Snooping
- Dynamic ARP Inspection (DAI)
- Port Security
- Rapid Spanning Tree (RSTP) with PortFast and BPDU Guard

---

## IP Addressing Design

The network uses **private IP addressing throughout**, simulating an internal enterprise WAN similar to MPLS.

- All sites communicate using private IP ranges
- OSPF is used to advertise routes across the entire network
- No public IP addressing is required within the lab

This reflects real-world enterprise environments where:

- MPLS or private WAN links are used between sites
- Internal traffic remains within the private network
- Routing is handled internally without reliance on internet-based connectivity

---

## Lab Environment

The lab is built using:

- EVE-NG (Network emulation)
- Cisco IOS routers and switches
- Virtual PCs for endpoint testing
- Windows Server 2008 R2 VM (used for extended testing)

---

## Objectives

- Implement VLAN segmentation for different departments
- Configure inter-VLAN routing using Layer 3 switches
- Deploy OSPF for dynamic routing across all sites
- Implement HSRP for gateway redundancy and high availability
- Configure Rapid Spanning Tree (RSTP) for efficient Layer 2 convergence
- Optimise Layer 2 topology using PortFast and BPDU Guard
- Apply Layer 2 security features including DHCP Snooping, DAI, and Port Security
- Configure EtherChannel for link aggregation and redundancy
- Apply ACLs for traffic filtering and segmentation
- Validate network behaviour through testing and failover scenarios

---

## Key Design Concepts

- **Hierarchical Network Design** (Core + Access layers)
- **Redundancy** using HSRP and dual core switches
- **Scalability** through dynamic routing (OSPF)
- **Segmentation** using VLANs
- **Enterprise WAN Simulation** using private MPLS-style routing

---

## Status

### ✅ Lab completed

The enterprise network lab has been fully implemented, configured, and tested.

This includes:

- Full device configuration across all routers and switches
- End-to-end connectivity validation
- OSPF route propagation across all sites
- HSRP failover testing at the core layer
- ACL enforcement between departments
- SSH management access across devices

The lab reflects a complete, working enterprise network design with both Layer 2 and Layer 3 security controls in place.

---

## Lab Documentation

- [Base Configuration](labs/00-base-config.md)
- [Core Configuration](labs/01-core-setup.md)
- [Access Layer Configuration](labs/02-access-layer.md)
- [Routing Configuration (OSPF)](labs/03-routing-ospf.md)
- [Branch Switches Configuration](labs/04-branch-switches.md)
- [Access Control Lists Configuration](labs/05-access-control-lists.md)
- [Testing & Validation](labs/06-testing.md)

---
