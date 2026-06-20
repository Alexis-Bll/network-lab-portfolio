# Enterprise Network Topologies

## Overview

This folder contains the topology diagrams for the **Enterprise Network Lab**.

The diagrams show how the lab is structured across two defined stages:

- **V1** - Completed private enterprise WAN design
- **V2** - Enterprise services expansion with internet edge connectivity and centralised services

The purpose of this folder is to visually support the lab documentation by showing how the network is built, how sites connect together, and how the design evolved from V1 into V2.

---

## Topology Versions

| Version | Status | Description | Files |
|---|---|---|---|
| [V1](v1/) | Completed | Private enterprise WAN design covering HQ, branch sites, routing, redundancy, VLANs, ACLs, and Layer 2 security | [V1 Draw.io PNG](v1/v1_ccna_lab.drawio.png), [V1 EVE-NG Screenshot](v1/v1-eve-ng-topology.png) |
| [V2](v2/) | In Progress | Expanded topology adding internet access, NAT, centralised services, Active Directory, and application server integration | [V2 Draw.io PNG](v2/v2_ccna_lab.drawio.png), [V2 EVE-NG Screenshot](v2/v2-eve-ng-topology.png) |

---

## V1 - Private Enterprise WAN Design

V1 represents the completed enterprise network foundation.

It focuses on internal network connectivity between a headquarters site and two branch offices using a private WAN/MPLS-style design.

### V1 Demonstrates

- HQ and branch office connectivity
- Collapsed core/access switching at HQ
- Redundant HQ core switches
- VLAN segmentation for Admin, Servers, Engineering, and Sales
- Inter-VLAN routing using SVIs
- OSPF routing across the enterprise WAN
- HSRP gateway redundancy
- EtherChannel between HQ core switches
- ACLs controlling traffic between departments
- Layer 2 security controls at the access layer

### V1 Topology Files

- [V1 Topology Folder](v1/)
- [V1 Draw.io Export](v1/v1_ccna_lab.drawio.png)
- [V1 EVE-NG Screenshot](v1/v1-eve-ng-topology.png)

---

## V2 - Enterprise Services Expansion

V2 builds on the V1 topology by adding services that are commonly found in real enterprise environments.

It keeps the same core network design but expands the lab with internet access, centralised services, and additional client/server infrastructure.

### V2 Demonstrates

- Internet edge connectivity from HQ
- NAT/PAT for outbound internet access
- Default route advertisement into OSPF
- Centralised DHCP services
- DNS services
- Active Directory Domain Services
- Domain-joined clients across multiple VLANs
- Group Policy testing
- Application server integration
- Additional client devices for more realistic testing

### V2 Topology Files

- [V2 Topology Folder](v2/)
- [V2 Draw.io Export](v2/v2_ccna_lab.drawio.png)
- [V2 EVE-NG Screenshot](v2/v2-eve-ng-topology.png)

---

## Design Progression

The topology progression shows how the lab developed from a network-only design into a more realistic enterprise environment.

### V1 Focus

V1 focuses on the core networking foundation:

- Private WAN connectivity
- Internal routing
- VLAN segmentation
- Gateway redundancy
- Layer 2 security
- Department-based access control

### V2 Focus

V2 expands the same design by adding enterprise services:

- Internet access
- NAT/PAT
- Centralised DHCP
- DNS
- Active Directory
- Group Policy
- Application server access

This shows how an enterprise network can evolve incrementally without replacing the original design.

---

## Directory Structure

```text
topology/
├── README.md
├── v1/
│   ├── README.md
│   ├── v1_ccna_lab.drawio.png
│   └── v1-eve-ng-topology.png
└── v2/
    ├── README.md
    ├── v2_ccna_lab.drawio.png
    └── v2-eve-ng-topology.png
```

---

## Related Documentation

- [Enterprise Network Lab Overview](../)
- [Lab Documentation](../labs/)
- [V1 Lab Documentation](../labs/v1/)
- [V2 Lab Documentation](../labs/v2/)
- [Device Configurations](../configs/)

---

## Summary

These topology diagrams provide a visual overview of the Enterprise Network Lab.

V1 shows the completed private enterprise WAN foundation, while V2 shows how the same network was expanded with internet connectivity and enterprise services.

Together, the diagrams help demonstrate network design, segmentation, routing, redundancy, security controls, and infrastructure service integration.

---
