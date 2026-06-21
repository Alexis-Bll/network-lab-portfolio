# Enterprise Network Topology - V2

## Overview

This folder contains the topology diagrams for **V2** of the Enterprise Network Lab.

V2 is the enterprise services expansion of the completed V1 network foundation. It keeps the original multi-site enterprise design and adds additional services commonly found in real business environments.

V2 introduces:

- Internet edge connectivity
- NAT/PAT on the HQ edge router
- Centralised DHCP services
- DNS services
- Active Directory Domain Services
- Domain-joined clients
- Group Policy testing
- Application server integration

V2 is the final planned expansion of this lab.

---

## V2 Status

**Status:** In Progress

The V2 topology has been built to expand the completed V1 network.

Some V2 services have already been implemented and documented, while application server integration, final testing, and configuration change documentation are still being completed.

---

## Network Diagram

![V2 Enterprise Network Topology](v2_ccna_lab.drawio.png)

The diagram above shows the logical design of the V2 enterprise network.

Additional topology file:

- [V2 EVE-NG topology screenshot](v2-eve-ng-topology.png)

---

## Relationship to V1

V2 does not replace the V1 topology.

Instead, it builds on the completed V1 network baseline.

| Area | V1 | V2 |
|---|---|---|
| HQ and branch topology | Completed | Reused |
| VLAN segmentation | Completed | Reused |
| OSPF routing | Completed | Extended with default route advertisement |
| HSRP redundancy | Completed | Retained |
| STP and EtherChannel | Completed | Retained |
| ACL segmentation | Completed | Retained and expanded where required |
| Layer 2 security | Completed | Adjusted for DHCP and DAI behaviour |
| Internet access | Not included | Added |
| NAT/PAT | Not included | Added |
| DHCP | Static addressing | Centralised DHCP introduced |
| DNS | Not included | Added |
| Active Directory | Not included | Added |
| Application services | Not included | Added |

This shows how an existing enterprise network can be expanded without redesigning the entire topology.

---

## What This Topology Demonstrates

The V2 topology demonstrates how infrastructure services can be added to a working enterprise network.

It includes:

- Multi-site enterprise network design
- HQ and branch connectivity
- Private WAN/MPLS-style transit network
- Simulated internet connectivity
- Internet edge design using HQ-R1
- NAT/PAT for outbound internet access
- Centralised DHCP from DC1
- DHCP relay across routed VLANs
- DNS and Active Directory services
- Domain-joined clients across multiple VLANs
- Application server placement
- Continued use of OSPF, HSRP, VLANs, ACLs, and Layer 2 security from V1

---

## Architecture Overview

| Area | Description |
|---|---|
| **HQ Site** | Contains dual core switches, access switches, admin clients, server VLAN, DC1, and application server |
| **Branch 1** | Engineering branch with router, access switch, and Engineering clients |
| **Branch 2** | Sales branch with router, access switch, and Sales clients |
| **Private WAN** | Simulated MPLS-style provider network using an ISP router |
| **Internet Edge** | HQ-R1 provides NAT/PAT and default route access to a simulated internet network |
| **Server Layer** | DC1 provides DHCP, DNS, Active Directory, and domain services |
| **Application Layer** | Internal application server used for service reachability and access testing |

---

## Network Layers

### HQ Core Layer

The HQ core layer remains inherited from V1.

It uses two Layer 3 switches:

- HQ-CSW1
- HQ-CSW2

The core switches provide:

- Inter-VLAN routing using SVIs
- HSRP default gateway redundancy
- OSPF routing towards the WAN edge
- Core switching and traffic distribution
- STP root bridge control
- EtherChannel between both core switches

The HQ core remains the routing and redundancy foundation for the internal VLANs.

---

### HQ Access Layer

The HQ access layer provides endpoint and server connectivity.

| Switch | VLAN | Purpose |
|---|---|---|
| HQ-ASW1 | VLAN 10 | Admin access switch |
| HQ-ASW2 | VLAN 10 | Admin access switch |
| HQ-SSW1 | VLAN 20 | Server access switch |
| HQ-SSW2 | VLAN 20 | Server access switch |

The access layer continues to provide:

- End-device connectivity
- VLAN segmentation
- Trunk connectivity to the core
- Layer 2 security controls
- Access for servers and domain services

---

### Branch Access Layer

The branch design remains simple and consistent with V1.

| Site | VLAN | Subnet | Purpose |
|---|---|---|---|
| Branch 1 | VLAN 30 | 192.168.30.0/24 | Engineering branch |
| Branch 2 | VLAN 40 | 192.168.40.0/24 | Sales branch |

Each branch contains:

- One branch router
- One Layer 2 access switch
- Local client devices
- One branch VLAN

The branch routers act as both the local default gateways and WAN edge devices.

---

### Private WAN Layer

The private WAN layer connects HQ and both branch sites.

Devices involved:

- HQ-R1
- HQ-R2
- ISP
- BR1
- BR2

The WAN layer provides:

- Routed connectivity between HQ and branches
- OSPF neighbour relationships
- Route propagation across all sites
- Redundant HQ WAN paths inherited from V1

The ISP router represents a simulated private WAN/MPLS-style provider network.

---

### Internet Edge Layer

V2 introduces internet edge connectivity through HQ-R1.

HQ-R1 acts as the NAT boundary between the internal enterprise network and the simulated external network.

The internet edge provides:

- NAT/PAT for outbound traffic
- Default route towards the simulated internet
- OSPF default route advertisement into the internal network
- Separation between internal enterprise networks and external connectivity

In this lab, internet access is simulated in EVE-NG. The external network is used to validate NAT and routing behaviour.

---

### Server Layer

The V2 server layer is located in the HQ server VLAN.

Key servers include:

| Server | Role |
|---|---|
| DC1 | DHCP, DNS, Active Directory Domain Services |
| Application Server | Internal application/service testing |

DC1 provides centralised infrastructure services for the lab.

This allows clients across different VLANs and branch sites to receive IP addressing, resolve names, join the domain, authenticate, and access internal resources.

---

## VLAN and IP Addressing Summary

### LAN Networks

| VLAN | Name / Purpose | Subnet | Default Gateway |
|---|---|---|---|
| VLAN 10 | Admin | 192.168.10.0/24 | 192.168.10.1 via HSRP |
| VLAN 20 | Servers | 192.168.20.0/24 | 192.168.20.1 via HSRP |
| VLAN 30 | Engineering Branch | 192.168.30.0/24 | 192.168.30.1 via BR1 |
| VLAN 40 | Sales Branch | 192.168.40.0/24 | 192.168.40.1 via BR2 |

### Server Addresses

| Device | VLAN | IP Address | Role |
|---|---|---|---|
| DC1 | VLAN 20 | 192.168.20.10 | DHCP, DNS, Active Directory |
| Application Server | VLAN 20 | To be documented | Internal application services |

### WAN / Transit Networks

The private WAN and routed core links use private `10.0.0.0/8` addressing with `/30` point-to-point subnets.

This keeps routed infrastructure links separate from user and server VLANs.

### External / Internet Network

V2 adds a simulated external network for NAT testing.

Example external subnet:

```text
203.0.113.0/30
```

HQ-R1 uses this network on its internet-facing interface.

---

## Internet Edge Design

HQ-R1 is used as the internet edge router in V2.

It provides:

- NAT inside interfaces facing the enterprise network
- NAT outside interface facing the simulated internet
- PAT overload for internal private addresses
- Default route towards the simulated internet
- Default route advertisement into OSPF

### NAT Design

Internal networks remain privately addressed.

The NAT policy translates internal traffic from:

```text
192.168.0.0/16
```

This allows Admin, Server, Engineering, and Sales networks to use outbound internet access through HQ-R1.

---

## DHCP and DNS Design

V2 introduces centralised DHCP and DNS from DC1.

DC1 is located in the HQ server VLAN:

```text
192.168.20.10
```

DC1 provides:

- DHCP scopes for client VLANs
- DNS services for the `lab.local` domain
- Active Directory-integrated DNS
- Name resolution for domain-joined clients

DHCP relay is required because clients exist in different VLANs and branch networks.

Layer 3 gateways use `ip helper-address` to forward DHCP requests to DC1.

---

## Active Directory Design

V2 introduces Active Directory Domain Services.

The lab domain is:

```text
lab.local
```

Active Directory provides:

- Centralised authentication
- Domain-joined clients
- Organisational Units
- Users and groups
- Group-based access control testing
- Group Policy testing

This makes the lab more realistic by showing how network connectivity supports identity and endpoint management services.

---

## Application Server Design

V2 includes an internal application server as the final service layer.

The application server is intended to demonstrate:

- Server placement in the HQ server environment
- Client access from different VLANs
- Internal service reachability
- Access control considerations
- End-to-end validation beyond basic ping testing

This section is still being completed as part of the V2 documentation.

---

## Redundancy Design

V2 retains the redundancy mechanisms from V1.

### Core Redundancy

- Dual HQ core switches
- HSRP virtual gateways
- STP root placement aligned with HSRP
- EtherChannel between core switches

### WAN Redundancy

- Dual HQ WAN routers
- Multiple routed paths between HQ core and WAN edge
- OSPF route recalculation during failures

### Internet Edge Note

The V2 internet edge is intentionally kept simple.

HQ-R1 acts as the NAT boundary for the lab. Internet edge high availability is not implemented in V2 because the focus is on demonstrating NAT, default routing, DHCP, DNS, Active Directory, and application services.

---

## Security and Segmentation

V2 keeps the segmentation model from V1.

Security controls include:

- VLAN segmentation
- ACL-based Engineering and Sales separation
- DHCP Snooping
- Dynamic ARP Inspection
- Port Security
- BPDU Guard
- Trunk VLAN restrictions

V2 also introduces additional security considerations around:

- DHCP trust boundaries
- DHCP relay behaviour
- DAI behaviour with dynamically addressed clients
- Domain-based access control
- Group Policy enforcement
- Application access control

---

## Key Design Choices

| Design Choice | Reason |
|---|---|
| Build on V1 instead of redesigning | Shows realistic incremental network expansion |
| Use HQ-R1 as NAT edge | Keeps internet breakout centralised at HQ |
| Use DC1 for DHCP, DNS, and AD DS | Reflects a common small enterprise server design |
| Keep branch design simple | Maintains focus on enterprise service integration |
| Use DHCP relay | Allows one central DHCP server to serve multiple VLANs |
| Keep OSPF in place | Preserves dynamic routing and route propagation |
| Retain V1 ACLs | Maintains Engineering and Sales segmentation |
| Add application server last | Validates that the network supports real internal services |

---

## Technologies Demonstrated

### Inherited from V1

- VLANs and trunking
- Inter-VLAN routing using SVIs
- OSPF dynamic routing
- HSRP gateway redundancy
- Rapid PVST+
- STP root bridge tuning
- LACP EtherChannel
- Access Control Lists
- DHCP Snooping
- Dynamic ARP Inspection
- Port Security
- PortFast and BPDU Guard
- SSH management

### Added in V2

- Internet edge connectivity
- NAT/PAT
- Default static route
- OSPF default route advertisement
- DHCP relay
- Centralised DHCP scopes
- DNS services
- Active Directory Domain Services
- Domain-joined clients
- Organisational Units
- Users and groups
- Group Policy Objects
- Application server integration

---

## V2 Scope

### Included in V2

- Internet access through HQ-R1
- NAT/PAT for internal networks
- Default route advertisement
- DHCP from DC1
- DNS from DC1
- Active Directory domain services
- Domain joins across VLANs
- Group Policy testing
- Application server integration
- Final V2 testing and validation

### Not Included in V2

- Additional lab versions after V2
- Firewall replacement
- Fortinet integration
- Wireless networking
- VPN services
- Cloud integration
- Monitoring platforms
- Full production hardening

These topics may be explored separately in the future, but they are outside the scope of this Enterprise Network Lab progression.

---

## Directory Structure

```text
topology/v2/
├── README.md
├── v2_ccna_lab.drawio.png
└── v2-eve-ng-topology.png
```

---

## Related Documentation

- [V2 Lab Documentation](../../labs/v2/)
- [01 - Internet Edge and NAT](../../labs/v2/01-internet-%26-edge.md)
- [02 - DHCP Services](../../labs/v2/02-dhcp-services.md)
- [03 - DNS and Domain Services](../../labs/v2/03-dns-%26-domain-services.md)
- [04 - Application Server Integration](../../labs/v2/04-application-server-integration.md)
- [05 - Testing and Validation](../../labs/v2/05-testing-validation.md)
- [V1 Topology](../v1/)
- [Topology Overview](../)
- [Device Configurations](../../configs/)
- [V2 Configuration Changes](../../configs/changes-v2/)

---

## Summary

The V2 topology shows how the completed V1 enterprise network foundation is expanded with internet connectivity and centralised services.

It demonstrates how routing, switching, redundancy, segmentation, NAT, DHCP, DNS, Active Directory, Group Policy, and application services can work together in a more realistic enterprise environment.

Once complete, V2 will represent the final planned state of the Enterprise Network Lab.
