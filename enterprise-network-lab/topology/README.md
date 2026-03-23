# Enterprise Network Topology

## Network Diagram

![Topology](ccna_lab.png)

This lab simulates a small enterprise network consisting of a headquarters (HQ) and two branch offices connected over a service provider WAN (MPLS). The design demonstrates core networking concepts including routing, redundancy, VLAN segmentation, and scalable architecture.

## Architecture

- **WAN**: Service Provider MPLS network connecting all sites  
- **Routing Protocol**: OSPF Area 0 across all routers  
- **HQ Design**: Collapsed core with dual core switches (CSW1/CSW2)  
- **Branch Design**: Single router and access switch per branch  

## Network Layers

- **Core Layer**: HQ-CSW1 and HQ-CSW2 (Layer 3 switches)  
- **Access Layer**: HQ-ASW1/2 (users) and HQ-SSW1/2 (servers)  
- **Edge/WAN Layer**: HQ-R1 and HQ-R2

## Branch Sites

Each branch site consists of:

- **Branch Router (BR1 / BR2)** connected to the WAN  
- **Access Switch (BR-SW1 / BR-SW2)** for end devices  
- End-user devices (e.g. Engineer PC, Sales PC)  

Branch sites connect to HQ via the service provider WAN (MPLS) and participate in OSPF Area 0.

## Redundancy

The network is designed with multiple layers of redundancy to ensure high availability and fault tolerance across both the HQ and branch sites:

- **Dual Core Switches (Collapsed Core Design)**:  
  Two Layer 3 core switches (HQ-CSW1 and HQ-CSW2) provide redundancy at the HQ core layer, ensuring continued operation if one switch fails.

- **HSRP (Hot Standby Router Protocol)**:  
  HSRP is configured on the core switches to provide default gateway redundancy for HQ VLANs. A virtual IP address is used as the gateway for end devices, allowing seamless failover between switches.

- **EtherChannel (LACP)**:  
  An EtherChannel is configured between HQ-CSW1 and HQ-CSW2 using LACP. This provides both increased bandwidth and link redundancy while preventing Spanning Tree blocking.

- **Spanning Tree Protocol (STP)**:  
  Rapid PVST+ is used at the HQ site to prevent Layer 2 loops and ensure fast convergence.  
  STP root roles are intentionally split:
  - HQ-CSW1 is configured as the primary root bridge for the Admin/Management VLAN  
  - HQ-CSW2 is configured as the primary root bridge for the Server VLAN  
  Each switch is configured as the secondary root for the opposing VLAN.

  STP tuning is applied only at HQ, as branch VLANs are locally switched and do not form extended Layer 2 domains with HQ.

- **Dual WAN Edge Routers**:  
  Two edge routers (HQ-R1 and HQ-R2) provide redundancy at the WAN edge, ensuring continued connectivity to the service provider network in the event of a router failure.

- **Redundant Core-to-Edge Connectivity**:  
  Each core switch is connected to both WAN edge routers, providing multiple paths between the LAN and WAN. This ensures traffic can still reach the WAN even if a single link or device fails.

- **OSPF Dynamic Routing (Area 0)**:  
  OSPF is used across all routers to provide dynamic routing and automatic failover. In the event of a link or device failure, OSPF recalculates routes to maintain connectivity.

- **Redundant Access Layer Uplinks**:  
  Access switches at HQ are connected to the core switches via trunk links, providing alternative paths and improving resilience within the LAN.

- **Service Provider WAN (MPLS)**:  
  The WAN is modelled as a service provider MPLS network, providing resilient connectivity between HQ and branch sites. Branch VLANs remain locally switched, with routing performed between sites.

## Technologies Used

The following technologies are implemented within this lab to demonstrate core networking concepts at a CCNA level:

- **OSPF (Open Shortest Path First)**:  
  OSPF Area 0 is used as the dynamic routing protocol across all routers, enabling automatic route calculation and failover between HQ and branch sites.

- **VLANs (Virtual Local Area Networks)**:  
  Network segmentation is implemented using VLANs to separate Sales, Engineering, Admin/Management, and Server traffic.

- **Inter-VLAN Routing (SVIs)**:  
  Layer 3 switching is used at the HQ core switches (CSW1 and CSW2) to route traffic between VLANs using Switch Virtual Interfaces (SVIs).

- **HSRP (Hot Standby Router Protocol)**:  
  HSRP is configured on the core switches to provide default gateway redundancy for HQ VLANs, ensuring high availability for end devices.

- **802.1Q Trunking**:  
  Trunk links are configured between switches to allow multiple VLANs to traverse the network.

- **EtherChannel (LACP)**:  
  Link aggregation is used between the core switches to increase bandwidth and provide redundancy while preventing Spanning Tree blocking.

- **Spanning Tree Protocol (Rapid PVST+)**:  
  Rapid PVST+ is implemented to prevent Layer 2 loops and ensure fast convergence. Root bridge roles are manually controlled for optimal traffic flow.

- **Static Routing (Edge/WAN)**:  
  Static or default routes may be used on WAN edge routers to direct traffic toward the service provider network.

- **IP Addressing and Subnetting**:  
  Structured IP addressing is used to logically separate network segments and support routing across the topology.

- **Default Gateway Redundancy**:  
  Provided using HSRP to ensure uninterrupted access to network resources in the event of device failure.

- **Layer 2 Switching**:  
  Access switches provide connectivity for end devices and support VLAN segmentation at both HQ and branch sites.

- **Service Provider WAN (MPLS Concept)**:  
  The WAN is modelled as an MPLS-based service provider network, allowing communication between geographically separated sites.
