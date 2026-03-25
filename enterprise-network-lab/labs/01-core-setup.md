# 01 - Core Layer Setup

## Objective

Configure the HQ core layer to provide VLAN segmentation, Layer 3 gateway redundancy, and resilient switching between the core and access layers.

This phase focuses on the two core switches:

- CSW1
- CSW2

---

## Core Layer Design

The core layer was built using two multilayer switches to provide:

- Centralised inter-VLAN routing
- Redundant default gateways using HSRP
- Layer 2 redundancy through STP
- Increased bandwidth and resiliency between the core switches using EtherChannel

The design uses:

- **VLAN 10** for Admin
- **VLAN 20** for Servers

CSW1 was configured as the preferred device for VLAN 10, while CSW2 was configured as the preferred device for VLAN 20. This allows traffic load-sharing across the two core switches instead of leaving one mostly idle.

---

## VLAN Configuration

The following VLANs were created on both core switches:

- **VLAN 10** - ADMIN
- **VLAN 20** - SERVERS

Example configuration:

```bash
vlan 10
 name ADMIN

vlan 20
 name SERVERS
```

## EtherChannel Between Core Switches

Two physical interfaces were bundled into a single EtherChannel link between CSW1 and CSW2 using **LACP**.

This provides:

- Link redundancy  
- Higher aggregated bandwidth  
- Simplified logical management  

---

## Important Note on 802.1Q Encapsulation

In this lab, trunking could not be enabled until the trunk encapsulation type was manually set.

The following command was required before enabling trunk mode:

```bash
switchport trunk encapsulation dot1q
```
