# Enterprise Network Lab

## Overview

This lab simulates a small enterprise network using technologies covered in the Cisco CCNA.

The design represents a realistic business environment with a headquarters (HQ), two branch offices, and an ISP. The network is built to demonstrate scalability, redundancy, and secure communication between sites.

---

## Topology

<p align="center">
  <img src="./topology/ccna_lab.png" width="800">
</p>

The topology includes:
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
- Access Control Lists (ACL)

---

## IP Addressing Design

The network uses **private IP addressing throughout**, simulating an internal enterprise WAN similar to MPLS.

- All sites communicate using private IP ranges
- OSPF is used to advertise routes across the entire network
- No public IP addressing is required within the lab

This reflects real-world enterprise environments where:
- MPLS or private WAN links are used between sites
- Internal traffic never leaves the private network
- Routing is handled internally rather than relying on internet-based connectivity

---

## Lab Environment

The lab is built using:

- EVE-NG (Network emulation)
- Cisco IOS routers and switches
- Virtual PCs for endpoint testing
- Windows Server 2008 R2 VM (for future services like DHCP/DNS)

---

## Objectives

- Implement VLAN segmentation for different departments
- Configure inter-VLAN routing using Layer 3 switches
- Deploy OSPF for dynamic routing across all sites
- Implement HSRP for gateway redundancy and high availability
- Configure Rapid Spanning Tree (RSTP) for efficient Layer 2 convergence
- Optimise Layer 2 topology using Spanning Tree enhancements (PortFast, BPDU Guard)
- Apply Layer 2 security features including DHCP Snooping, Dynamic ARP Inspection (DAI), and Port Security
- Configure EtherChannel for link aggregation and redundancy
- Apply Access Control Lists (ACLs) for traffic filtering and segmentation
- Validate network resilience through failover and troubleshooting scenarios

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

While the core lab has been fully implemented and is operational, a small number of final steps are currently in progress to enhance the overall quality and completeness of the project.

These include:

* Organising and publishing final device configurations
* Performing additional validation and testing (e.g. failover scenarios for HSRP and redundancy)
* Making minor design refinements and optimisations

These updates are focused on improving clarity, validation, and presentation rather than core functionality, which is already complete.

The lab will continue to be refined to better reflect real-world deployment and testing practices.


---

## Lab Documentation

- [Base Configuration](labs/00-base-config.md)
- [Core Configuration](labs/01-core-setup.md)
- [Access Layer Configuration](labs/02-access-layer.md)
- [Routing Configuration (OSPF)](labs/03-routing-ospf.md)
- [Branch Switches Configuration](labs/04-branch-switches.md)
- [Access Control Lists Configuration](labs/05-access-control-lists.md)
- [Testing](labs/06-testing.md)

---
