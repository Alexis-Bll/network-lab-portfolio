# Enterprise Network Topology

## Network Diagram

![Topology](ccna_lab.png)

This lab simulates a small enterprise network consisting of a headquarters (HQ) and two branch offices connected over a service provider WAN (MPLS-style).

The design demonstrates how enterprise networks use hierarchical architecture, private WAN connectivity, and dynamic routing to provide scalable and resilient communication between sites.

---

## Architecture Overview

- **WAN**: Simulated service provider network (MPLS-style using private IP addressing)  
- **Routing Protocol**: OSPF Area 0 across all Layer 3 devices  
- **HQ Design**: Collapsed core using dual Layer 3 switches  
- **Branch Design**: Single router and access switch per branch
- **WAN Edge Devices**: HQ-R1, HQ-R2, BR1, BR2
- **Traffic Flow**: Inter-VLAN routing is performed at the core layer (SVIs), while inter-site traffic is routed via OSPF across the WAN edge

---

## Network Layers

### Core Layer (HQ)
- HQ-CSW1
- HQ-CSW2  
Layer 3 switches providing:
- Inter-VLAN routing (SVIs)
- HSRP gateway redundancy
- Core switching and traffic distribution

---

### Access Layer
- HQ-ASW1 / HQ-ASW2 → Admin VLAN (VLAN 10)
- HQ-SSW1 / HQ-SSW2 → Server VLAN (VLAN 20)
- BR-SW1 → Engineering VLAN (VLAN 30)
- BR-SW2 → Sales VLAN (VLAN 40)

Provides:
- End-device connectivity
- VLAN segmentation
- Layer 2 security is applied at the access layer using DHCP Snooping, Dynamic ARP Inspection (DAI), and Port Security to protect against common network attacks

---

### WAN / Edge Layer

* HQ-R1
* HQ-R2
* BR1
* BR2
* ISP Router

Provides:

* Connectivity between headquarters and branch sites
* WAN edge routing for both HQ and branch networks
* Transit network simulating a service provider MPLS core using private addressing
* OSPF adjacency and route propagation across all sites

### Design Notes

* Both **HQ and branch routers operate as WAN edge devices**, connecting their local networks to the service provider WAN
* The ISP router acts as a **simulated MPLS core**, providing transit between sites
* Branch routers (BR1 / BR2) serve as:

  * Default gateways for branch LANs
  * Edge devices connecting to the WAN

* The design reflects a balance between performance, redundancy, and operational simplicity, aligning with real-world enterprise network design principles
---

## Branch Sites

Each branch site consists of:

### Branch 1 (Engineering)
- Network: 192.168.30.0/24
- VLAN 30
- Devices: BR1 + BR-SW1 + Engineering PC

### Branch 2 (Sales)
- Network: 192.168.40.0/24
- VLAN 40
- Devices: BR2 + BR-SW2 + Sales PC

### Key Design Choices

- Each branch uses a **single VLAN**, reflecting a simplified small-site deployment
- The **branch router acts as both the default gateway and WAN edge device**
- Layer 2 switching is kept minimal at branch sites to reduce complexity
- Routing decisions are handled at the branch edge (router), aligning with real-world branch deployments

---

## IP Addressing Strategy

The entire network uses **private IP addressing**, simulating an enterprise MPLS WAN:

### LAN Networks
- VLAN 10 (Admin): 192.168.10.0/24
- VLAN 20 (Servers): 192.168.20.0/24
- Engineering Branch: 192.168.30.0/24
- Sales Branch: 192.168.40.0/24

### WAN / Transit Networks
- /30 subnets used for point-to-point links (efficient and scalable)

### Design Justification
- Reflects real enterprise WAN/MPLS deployments
- No public IP addressing required internally
- All routing handled via OSPF

---

## Redundancy Design

The network includes multiple layers of redundancy:

### Core Redundancy
- Dual core switches (HQ-CSW1 / HQ-CSW2)
- Load sharing using HSRP:
  - HQ-CSW1 active for VLAN 10
  - HQ-CSW2 active for VLAN 20

### Layer 2 Redundancy
- EtherChannel (LACP) between core switches
- Dual uplinks from access switches to both core switches
- Spanning Tree root bridge placement aligned with HSRP active gateways to optimise traffic flow and avoid suboptimal Layer 2 paths

### WAN Redundancy
- Dual HQ routers (HQ-R1 / HQ-R2)
- Each core switch connects to both routers
- Multiple paths to the WAN
- Failover is achieved through OSPF convergence and redundant physical paths

### Routing Redundancy
- OSPF dynamically recalculates routes
- Equal-cost load balancing (ECMP)
- Automatic failover on link/device failure

---

## Technologies Implemented

- OSPF (Dynamic routing across all sites)
- VLANs (Network segmentation)
- Inter-VLAN Routing (SVIs)
- HSRP (Gateway redundancy)
- EtherChannel (Link aggregation)
- 802.1Q Trunking
- Rapid PVST+ (Spanning Tree)
- Spanning Tree Enhancements (Portfast, BPDU Guard)
- ACLs (Traffic filtering between departments)
- DHCP Snooping & DAI (Layer 2 security)
- Port Security (Access layer protection)

---

## Key Design Principles

- **Hierarchical network design** (Core + Access)
- **Separation of Layer 2 and Layer 3 roles**
- **Private WAN/MPLS-style architecture**
- **Redundancy at every layer**
- **Security at the network edge**
- **Scalability through dynamic routing (OSPF)**

---

## Scope

This lab focuses on private WAN connectivity and internal enterprise design. Internet edge services such as NAT and public connectivity are not included in this version and will be introduced in a future iteration.

## Summary

This topology represents a realistic enterprise network design, demonstrating how multiple networking technologies integrate to provide:

- Secure segmentation of departments
- High availability through redundancy
- Scalable multi-site connectivity
- Efficient routing across a private WAN

The lab reflects both CCNA-level concepts and real-world enterprise design principles.
