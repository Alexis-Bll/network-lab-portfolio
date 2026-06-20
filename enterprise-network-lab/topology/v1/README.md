# Enterprise Network Topology - V1

## Overview

This folder contains the topology diagrams for **V1** of the Enterprise Network Lab.

V1 represents the completed private enterprise WAN design. It focuses on the core networking foundation of the lab, including routing, switching, VLAN segmentation, redundancy, access control, and Layer 2 security.

V2 builds on this topology later by adding internet access, NAT, DHCP, DNS, Active Directory, Group Policy, and application server integration.

---

## Network Diagram

![V1 Enterprise Network Topology](v1_ccna_lab.drawio.png)

The diagram above shows the logical design of the V1 enterprise network.

Additional topology file:

- [EVE-NG topology screenshot](v1-eve-ng-topology.png)

---

## What This Topology Demonstrates

This topology demonstrates a complete CCNA-level enterprise network foundation, including:

- Multi-site network design with HQ and branch sites
- Collapsed core/access switching at HQ
- Redundant HQ core switches
- Private WAN/MPLS-style transit network
- Dynamic routing across all routed devices using OSPF
- VLAN segmentation for different departments
- Inter-VLAN routing using SVIs at the HQ core
- HSRP gateway redundancy for HQ VLANs
- EtherChannel between HQ core switches
- ACLs controlling traffic between Engineering and Sales
- Layer 2 security controls at the access layer

---

## Architecture Overview

| Area | Description |
|---|---|
| **HQ Site** | Contains dual Layer 3 core switches, access switches, admin PCs, and server devices |
| **Branch 1** | Engineering branch with a router, access switch, and Engineering PC |
| **Branch 2** | Sales branch with a router, access switch, and Sales PC |
| **WAN / Transit** | Simulated private WAN/MPLS-style provider network using an ISP router |
| **Routing Protocol** | OSPF Area 0 across routers and Layer 3 core switches |
| **Redundancy** | HSRP, EtherChannel, dual HQ routers, and redundant routed paths |
| **Security Controls** | ACLs, DHCP Snooping, Dynamic ARP Inspection, Port Security, PortFast, and BPDU Guard |

---

## Network Layers

### HQ Core Layer

The HQ core is built using two Layer 3 switches:

- HQ-CSW1
- HQ-CSW2

The core switches provide:

- Inter-VLAN routing for HQ VLANs using SVIs
- HSRP default gateway redundancy
- OSPF routing towards the WAN edge routers
- Core switching and traffic distribution
- STP root bridge control
- LACP EtherChannel between both core switches

In this design, HQ-CSW1 is the preferred gateway/root path for VLAN 10, while HQ-CSW2 is the preferred gateway/root path for VLAN 20.

---

### HQ Access Layer

The HQ access layer provides endpoint and server connectivity.

| Switch | VLAN | Purpose |
|---|---|---|
| HQ-ASW1 | VLAN 10 | Admin access switch |
| HQ-ASW2 | VLAN 10 | Admin access switch |
| HQ-SSW1 | VLAN 20 | Server access switch |
| HQ-SSW2 | VLAN 20 | Server access switch |

The HQ access switches provide:

- End-device connectivity
- VLAN segmentation
- 802.1Q trunking towards the core switches
- Layer 2 security controls
- Redundant uplinks to both core switches

---

### Branch Access Layer

Each branch site uses a simplified small-office design with one router and one access switch.

| Site | VLAN | Subnet | Devices |
|---|---|---|---|
| Branch 1 - Engineering | VLAN 30 | 192.168.30.0/24 | BR1, BR-SW1, Engineering PC |
| Branch 2 - Sales | VLAN 40 | 192.168.40.0/24 | BR2, BR-SW2, Sales PC |

At each branch, the router acts as both:

- The default gateway for the local branch LAN
- The WAN edge device connecting the branch to the provider network

This keeps the branch design simple and reflects a small-site deployment.

---

### WAN / Edge Layer

The WAN/edge layer contains:

- HQ-R1
- HQ-R2
- BR1
- BR2
- ISP router

This layer provides:

- Connectivity between HQ and branch sites
- Simulated private WAN/MPLS-style transit
- OSPF neighbour relationships between routed devices
- Route propagation across all sites
- Redundant paths from HQ towards the WAN

The ISP router acts as the simulated service provider transit router. It does not provide public internet access in V1.

---

## VLAN and IP Addressing Summary

### LAN Networks

| VLAN | Name / Purpose | Subnet | Default Gateway |
|---|---|---|---|
| VLAN 10 | Admin | 192.168.10.0/24 | 192.168.10.1 via HSRP |
| VLAN 20 | Servers | 192.168.20.0/24 | 192.168.20.1 via HSRP |
| VLAN 30 | Engineering Branch | 192.168.30.0/24 | 192.168.30.1 via BR1 |
| VLAN 40 | Sales Branch | 192.168.40.0/24 | 192.168.40.1 via BR2 |

### Router and Switch Loopbacks

| Device | Loopback |
|---|---|
| HQ-CSW1 | 1.1.1.1/32 |
| HQ-CSW2 | 2.2.2.2/32 |
| HQ-R1 | 3.3.3.3/32 |
| HQ-R2 | 4.4.4.4/32 |
| ISP | 5.5.5.5/32 |
| BR1 | 6.6.6.6/32 |
| BR2 | 7.7.7.7/32 |

### WAN / Transit Networks

The WAN and routed core links use private `10.0.0.0/8` addressing with `/30` point-to-point subnets.

This keeps the addressing clear by separating:

- `192.168.x.x` networks for LAN/VLAN subnets
- `10.x.x.x` networks for routed transit links
- `/32` loopbacks for device identification and routing tests

---

## Redundancy Design

V1 includes redundancy across multiple areas of the topology.

### Gateway Redundancy

HSRP is used on the HQ core switches to provide resilient default gateways for HQ VLANs.

| VLAN | Virtual Gateway | Preferred Active Device |
|---|---|---|
| VLAN 10 | 192.168.10.1 | HQ-CSW1 |
| VLAN 20 | 192.168.20.1 | HQ-CSW2 |

This provides gateway redundancy while also allowing basic load sharing between the two core switches.

---

### Layer 2 Redundancy

Layer 2 redundancy is provided through:

- Dual uplinks from access switches to the core switches
- LACP EtherChannel between HQ-CSW1 and HQ-CSW2
- Rapid PVST+ for loop prevention
- STP root bridge placement aligned with HSRP active gateways

This helps avoid Layer 2 loops while keeping predictable forwarding paths.

---

### WAN Redundancy

WAN redundancy is provided through:

- Dual HQ WAN routers
- Multiple routed links between the HQ core and HQ routers
- OSPF route recalculation if a routed path fails
- Equal-cost route options where available

This allows the network to maintain connectivity if a link or routed path fails.

---

## Security Design

V1 includes security controls at both Layer 2 and Layer 3.

### Layer 2 Security

Layer 2 security is applied mainly on access switches and includes:

- DHCP Snooping
- Dynamic ARP Inspection
- Port Security
- PortFast on access ports
- BPDU Guard on access ports
- Trunk hardening using allowed VLAN lists

These controls help protect the access layer from common Layer 2 issues such as rogue DHCP servers, ARP spoofing, accidental loops, and unauthorised endpoint changes.

### Layer 3 Security

Layer 3 traffic filtering is provided using ACLs.

In V1, ACLs are used to:

- Block Engineering-to-Sales traffic
- Block Sales-to-Engineering traffic
- Allow both departments to access HQ resources

This demonstrates basic inter-department traffic control without blocking required business access to central services.

---

## Routing Design

OSPF Area 0 is used across the entire V1 routed network.

OSPF is used to advertise:

- HQ VLAN networks
- Branch LAN networks
- WAN transit networks
- Device loopbacks

This allows dynamic route learning between HQ, the simulated ISP/WAN provider, and both branch sites.

V1 does not use NAT or internet breakout. All connectivity is internal to the private enterprise lab.

---

## Key Design Choices

| Design Choice | Reason |
|---|---|
| Collapsed core/access design | Keeps the lab realistic but manageable for a CCNA-level enterprise topology |
| Dual HQ core switches | Provides gateway, switching, and path redundancy |
| HSRP for HQ VLANs | Maintains default gateway availability during a core switch failure |
| STP root alignment with HSRP | Keeps Layer 2 forwarding paths aligned with active gateways |
| OSPF for routing | Provides scalable dynamic routing and automatic route recalculation |
| Single VLAN per branch | Keeps the branch design simple and focused |
| ACLs between branch departments | Demonstrates segmentation and traffic control |
| Layer 2 security at access layer | Adds realistic protection against common access-layer risks |

---

## Technologies Implemented

- VLANs and trunking
- Inter-VLAN routing using SVIs
- OSPF dynamic routing
- HSRP gateway redundancy
- Rapid PVST+
- STP root bridge tuning
- EtherChannel using LACP
- Access Control Lists
- DHCP Snooping
- Dynamic ARP Inspection
- Port Security
- PortFast
- BPDU Guard
- SSH management access

---

## Scope

V1 focuses on the completed private enterprise WAN foundation.

Included in V1:

- Internal routing
- VLAN segmentation
- HQ redundancy
- Branch connectivity
- ACL-based traffic filtering
- Layer 2 security
- Connectivity and failover validation

Not included in V1:

- Internet access
- NAT/PAT
- Centralised DHCP from Windows Server
- DNS services
- Active Directory
- Group Policy
- Application server integration

Those services are introduced in V2.

---

## Related Documentation

- [V1 Lab Documentation](../../labs/v1/)
- [V1 Device Configurations](../../configs/)
- [Topology Overview](../)
- [V2 Topology](../v2/)

---

## Summary

This V1 topology represents the completed enterprise network foundation of the lab.

It demonstrates how routing, switching, redundancy, segmentation, access control, and Layer 2 security can work together in a multi-site enterprise network.

This version provides the stable network base that V2 later expands with internet connectivity and enterprise infrastructure services.

---
