# Enterprise Network Lab

## Overview

This project simulates a small enterprise network based on technologies covered in the Cisco CCNA.

The lab is designed to represent a realistic business environment with a headquarters (HQ), two branch offices, and WAN connectivity. It demonstrates scalability, redundancy, and secure communication between sites using industry-relevant networking practices.

The project is split into two versions to show progression in both design and implementation:

- **V1** – Core enterprise network (completed)
- **V2** – Enhanced enterprise network with internet connectivity and services (in progress)

---

## Topology

<p align="center">
  <img src="./topology/v2/v2_ccna_lab.drawio.png" width="800">
</p>

### The topology includes:

- HQ with core and access layer switching
- Two branch offices (Sales and Engineering)
- Private WAN (MPLS-style) connectivity between sites
- Public internet connectivity via HQ edge (V2)
- End devices and servers for testing

---

## Technologies Used

### Core Technologies (V1)

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

### Additional Technologies (V2)

- NAT (Network Address Translation)
- Internet edge connectivity
- Centralised services (DHCP, DNS, Active Directory)
- Application server integration

---

## IP Addressing Design

The network primarily uses **private IP addressing**, simulating an internal enterprise WAN similar to MPLS.

- All internal communication uses private IP ranges
- OSPF is used to advertise routes across the network
- WAN links use /30 subnets for efficient point-to-point communication

In **V2**, external connectivity is introduced:

- NAT is used at the HQ edge to allow internal devices to access the public internet
- Internal addressing remains private while external access is translated

---

## Lab Environment

The lab is built using:

- EVE-NG (Network emulation)
- Cisco IOS routers and switches
- Virtual PCs for endpoint testing
- Windows Server 2008 R2 VM (used for extended testing and services)

---

## Objectives

### V1 Objectives

- Implement VLAN segmentation for different departments
- Configure inter-VLAN routing using Layer 3 switches
- Deploy OSPF for dynamic routing across all sites
- Implement HSRP for gateway redundancy and high availability
- Configure Rapid Spanning Tree (RSTP) for efficient Layer 2 convergence
- Apply Layer 2 security features (DHCP Snooping, DAI, Port Security)
- Configure EtherChannel for redundancy
- Apply ACLs for traffic filtering
- Validate failover and connectivity

### V2 Objectives

- Introduce internet connectivity via an ISP edge
- Implement NAT (PAT) for outbound traffic
- Deploy centralised services (DHCP, DNS, Active Directory)
- Replace static addressing with dynamic host configuration
- Improve realism of enterprise network design

---

## Key Design Concepts

- **Hierarchical Network Design** (Core + Access layers)
- **Redundancy** using HSRP and dual core switches
- **Scalability** through dynamic routing (OSPF)
- **Segmentation** using VLANs
- **Enterprise WAN Simulation** using private MPLS-style routing
- **Internet Edge Design and NAT** (V2)

---

## Status

### ✅ V1 – Completed

The original enterprise network lab has been fully implemented and tested, including:

- Full configuration of all routers and switches
- End-to-end connectivity validation
- OSPF route propagation across all sites
- HSRP failover testing
- ACL enforcement between departments
- Layer 2 security implementation
- SSH management access

---

### 🚧 V2 – In Progress

The enhanced version of the lab is currently being developed and includes:

- Internet connectivity via HQ edge
- NAT configuration for external access
- Deployment of centralised services
- Additional testing and validation

---

## Lab Documentation

### V1

- [Base Configuration](labs/v1/00-base-config.md)
- [Core Configuration](labs/v1/01-core-setup.md)
- [Access Layer Configuration](labs/v1/02-access-layer.md)
- [Routing Configuration (OSPF)](labs/v1/03-routing-ospf.md)
- [Branch Switches Configuration](labs/v1/04-branch-switches.md)
- [Access Control Lists Configuration](labs/v1/05-access-control-lists.md)
- [Testing & Validation](labs/v1/06-testing.md)

### V2

Documentation will be added as the lab progresses.

---
