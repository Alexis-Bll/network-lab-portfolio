# Enterprise Network Lab - V1

## Overview

V1 is the completed foundation of the **Enterprise Network Lab**.

This version focuses on building a private multi-site enterprise network using CCNA-level routing, switching, redundancy, segmentation, access control, and Layer 2 security technologies.

The lab simulates a business network with:

- A headquarters site
- Two branch sites
- A private WAN/MPLS-style provider network
- Department-based VLAN segmentation
- Redundant HQ core switches
- Dynamic routing between all sites
- Access control between departments
- Layer 2 security at the access layer
- End-to-end testing and validation

V1 provides the stable network foundation that V2 later builds on with internet access and enterprise services.

---

## V1 Status

**Status:** Completed

V1 has been configured, tested, and documented.

The current review stage is focused on polishing the documentation, checking links, and ensuring the written documentation accurately matches the final working configurations.

---

## Lab Objectives

The objectives of V1 were to:

- Build a realistic multi-site enterprise network
- Configure base settings across all routers and switches
- Implement VLAN segmentation for different departments
- Configure inter-VLAN routing using Layer 3 switch SVIs
- Provide HQ gateway redundancy using HSRP
- Tune Spanning Tree to align with the HSRP design
- Configure EtherChannel between the HQ core switches
- Deploy OSPF dynamic routing across the WAN
- Connect two branch sites to the HQ network
- Apply ACLs to restrict Engineering and Sales traffic
- Implement Layer 2 security features
- Validate connectivity, routing, failover, and access control

---

## Topology Summary

V1 includes the following network areas:

| Area | Devices | Purpose |
|---|---|---|
| HQ Core | HQ-CSW1, HQ-CSW2 | Inter-VLAN routing, HSRP, OSPF, STP root control, EtherChannel |
| HQ Access | HQ-ASW1, HQ-ASW2, HQ-SSW1, HQ-SSW2 | Admin and server endpoint connectivity |
| WAN / Edge | HQ-R1, HQ-R2, ISP, BR1, BR2 | Private WAN routing between HQ and branches |
| Branch 1 | BR1, BR-SW1, Engineering PC | Engineering branch network |
| Branch 2 | BR2, BR-SW2, Sales PC | Sales branch network |

The topology uses a collapsed core/access design at HQ, with branch routers acting as both the branch LAN default gateways and WAN edge devices.

---

## VLAN and IP Addressing Summary

| VLAN | Name / Purpose | Subnet | Default Gateway |
|---|---|---|---|
| VLAN 10 | Admin | 192.168.10.0/24 | 192.168.10.1 via HSRP |
| VLAN 20 | Servers | 192.168.20.0/24 | 192.168.20.1 via HSRP |
| VLAN 30 | Engineering Branch | 192.168.30.0/24 | 192.168.30.1 via BR1 |
| VLAN 40 | Sales Branch | 192.168.40.0/24 | 192.168.40.1 via BR2 |

WAN and routed transit links use private `10.0.0.0/8` addressing with `/30` point-to-point subnets.

Loopback interfaces are used for router and Layer 3 switch identification, OSPF router IDs, and testing.

---

## Technologies Implemented

V1 demonstrates the following technologies:

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
- Connectivity and failover testing

---

## Documentation Structure

The V1 documentation is split into phases to show how the network was built and validated.

| Phase | Document | Description |
|---|---|---|
| 00 | [Base Configuration](00-base-config.md) | Initial hostname, management, SSH, and baseline device settings |
| 01 | [Core Setup](01-core-setup.md) | HQ core switches, VLANs, SVIs, HSRP, STP, and EtherChannel |
| 02 | [Access Layer](02-access-layer.md) | HQ access switches, trunking, access ports, and Layer 2 security |
| 03 | [Routing with OSPF](03-routing-ospf.md) | OSPF routing across HQ, WAN, ISP, and branch routers |
| 04 | [Branch Switches](04-branch-switches.md) | Branch access switch configuration for Engineering and Sales |
| 05 | [Access Control Lists](05-access-control-lists.md) | ACLs controlling traffic between Engineering and Sales |
| 06 | [Testing and Validation](06-testing.md) | End-to-end testing, routing checks, SSH validation, and failover testing |

---

## Recommended Reading Order

To understand the lab properly, review the documents in this order:

1. [Base Configuration](00-base-config.md)
2. [Core Setup](01-core-setup.md)
3. [Access Layer](02-access-layer.md)
4. [Routing with OSPF](03-routing-ospf.md)
5. [Branch Switches](04-branch-switches.md)
6. [Access Control Lists](05-access-control-lists.md)
7. [Testing and Validation](06-testing.md)

This follows the same order in which the lab was designed, configured, secured, and tested.

---

## Key Design Decisions

### Collapsed Core/Access Design

The HQ site uses two Layer 3 core switches to provide inter-VLAN routing, redundancy, and traffic distribution.

This keeps the topology realistic but manageable for a CCNA-level enterprise lab.

### HSRP Gateway Redundancy

HSRP is configured on the HQ core switches to provide resilient default gateways for the Admin and Server VLANs.

| VLAN | Virtual Gateway | Preferred Active Device |
|---|---|---|
| VLAN 10 | 192.168.10.1 | HQ-CSW1 |
| VLAN 20 | 192.168.20.1 | HQ-CSW2 |

This allows basic load sharing while maintaining gateway redundancy.

### STP Alignment

Rapid PVST+ is tuned so that the STP root bridge placement aligns with the preferred HSRP active gateway for each VLAN.

This helps keep Layer 2 forwarding paths predictable and avoids unnecessary traffic crossing between core switches.

### OSPF Dynamic Routing

OSPF Area 0 is used across the routed network.

OSPF advertises:

- HQ VLAN networks
- Branch LAN networks
- WAN transit networks
- Device loopbacks

This allows all sites to dynamically learn routes without relying on static routing.

### Branch Design

Each branch uses a simple router-and-switch design.

The branch router acts as:

- The default gateway for the local branch VLAN
- The WAN edge device connecting the branch to the provider network

This reflects a small branch office deployment and keeps the lab focused.

### ACL-Based Segmentation

ACLs are used to control traffic between Engineering and Sales.

The design allows both departments to reach HQ resources while blocking direct communication between the Engineering and Sales branch networks.

### Layer 2 Security

Layer 2 security is applied mainly at the access layer.

Controls include:

- DHCP Snooping
- Dynamic ARP Inspection
- Port Security
- PortFast
- BPDU Guard
- Trunk VLAN restrictions

These features help protect against common access-layer risks such as rogue DHCP servers, ARP spoofing, accidental loops, and unauthorised endpoint changes.

---

## Validation Summary

V1 testing confirms:

- End-to-end connectivity between required networks
- OSPF neighbour relationships and route propagation
- HSRP gateway redundancy
- STP root bridge placement
- EtherChannel operation
- ACL enforcement between Engineering and Sales
- SSH management access
- Layer 2 security configuration
- Failover behaviour during simulated outages

Full testing evidence is documented in:

- [06 - Testing and Validation](06-testing.md)

---

## V1 Scope

### Included in V1

- Private enterprise WAN design
- HQ and branch connectivity
- VLAN segmentation
- Inter-VLAN routing
- Dynamic routing with OSPF
- HSRP gateway redundancy
- STP tuning
- EtherChannel
- ACL-based segmentation
- Layer 2 security
- SSH management
- Connectivity and failover validation

### Not Included in V1

- Internet access
- NAT/PAT
- Centralised DHCP from Windows Server
- DNS services
- Active Directory
- Group Policy
- Application server integration

These services are introduced in V2.

---

## Related Files

### Topology

- [V1 Topology](../../topology/v1/)
- [Topology Overview](../../topology/)

### Configurations

- [Device Configurations](../../configs/)

### Project Overview

- [Enterprise Network Lab Overview](../../)
- [Main Portfolio README](../../../)

---

## Summary

V1 represents the completed CCNA-level enterprise network foundation of the project.

It demonstrates the ability to design, configure, secure, troubleshoot, and validate a realistic multi-site network using routing, switching, redundancy, segmentation, ACLs, and Layer 2 security controls.

This version provides the stable baseline that V2 later expands with internet connectivity and enterprise infrastructure services.
