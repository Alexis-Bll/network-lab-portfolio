# Enterprise Network Topology

## Network Diagram

![Topology](CCNA_lab.png)

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

- Dual core switches at HQ  
- EtherChannel (LACP) between CSW1 and CSW2  
- Dual WAN edge routers

## Technologies Used

- OSPF (Area 0)
- VLANs and 802.1Q trunking
- EtherChannel (LACP)
