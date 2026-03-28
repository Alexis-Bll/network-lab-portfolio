# 01 - Core Layer Setup

## Objective

The objective of this phase is to configure the HQ core layer to provide:

- VLAN segmentation
- Inter-VLAN routing
- Default gateway redundancy
- Resilient Layer 2 and Layer 3 connectivity

This phase focuses on the two core switches:

- HQ-CSW1
- HQ-CSW2

---

## Topology Overview

![HQ Topology](../topology/eve-ng-topology.png)

---

## Core Layer Design

The core layer is built using two multilayer switches to provide high availability and efficient traffic forwarding.

Key design features:
- Centralised inter-VLAN routing using SVIs
- HSRP for redundant default gateways
- STP tuning for optimal Layer 2 forwarding
- EtherChannel (LACP) for increased bandwidth and redundancy

### VLAN Design
- VLAN 10 → Admin
- VLAN 20 → Servers
To optimise resource utilisation, traffic is load-balanced across both core switches:
- HQ-CSW1 → Active for VLAN 10
- HQ-CSW2 → Active for VLAN 20

This prevents one switch from remaining idle and improves overall efficiency.

---

## VLAN Configuration

The following VLANs were created on both core switches:

- **VLAN 10** - ADMIN
- **VLAN 20** - SERVERS

### Example configuration:

```bash
vlan 10
 name ADMIN

vlan 20
 name SERVERS
```
---

## EtherChannel Between Core Switches

Two physical interfaces were bundled into a single EtherChannel link between HQ-CSW1 and HQ-CSW2 using **LACP**.

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
This behaviour is typical of older Cisco IOS platforms that support multiple encapsulation types.

---

## Part 2

### HQ-CSW1 EtherChannel Configuration

```bash
interface range gi0/2 - 3
 switchport trunk encapsulation dot1q
 switchport mode trunk
 channel-group 1 mode active

interface port-channel 1
 switchport mode trunk
```

### HQ-CSW2 EtherChannel Configuration

```bash
interface range gi0/2 - 3
 switchport trunk encapsulation dot1q
 switchport mode trunk
 channel-group 1 mode active

interface port-channel 1
 switchport mode trunk
```
---

## Trunk Links to Access Switches

Each access switch is dual-homed to both core switches for redundancy.

Uplink interfaces were configured as trunk ports to allow multiple VLANs to traverse the links.

### Example

```bash
interface range gi1/0 - 3
 switchport trunk encapsulation dot1q
 switchport mode trunk
```
Interface numbering may vary depending on the lab topology and switch image used.

---

## Inter-VLAN Routing (SVIs)

Switch Virtual Interfaces (SVIs) were created to provide Layer 3 gateways.

### HQ-CSW1 Configuration

```bash
ip routing

interface vlan 10
 ip address 192.168.10.2 255.255.255.0
 standby 10 ip 192.168.10.1
 standby 10 priority 110
 standby 10 preempt

interface vlan 20
 ip address 192.168.20.2 255.255.255.0
 standby 20 ip 192.168.20.1
 standby 20 priority 90
 standby 20 preempt
```

---

## Part 3

### HQ-CSW2 Configuration

```bash
ip routing

interface vlan 10
 ip address 192.168.10.3 255.255.255.0
 standby 10 ip 192.168.10.1
 standby 10 priority 90
 standby 10 preempt

interface vlan 20
 ip address 192.168.20.3 255.255.255.0
 standby 20 ip 192.168.20.1
 standby 20 priority 110
 standby 20 preempt
```

---

## HSRP Design

HSRP provides redundant default gateways:

- VLAN 10
- Gateway: 192.168.10.1
- Active: HQ-CSW1
- Standby: HQ-CSW2
- VLAN 20
- Gateway: 192.168.20.1
- Active: HQ-CSW2
- Standby: HQ-CSW1

This enables load sharing across both core switches.

---

## STP Root Placement

Spanning Tree was tuned to align with HSRP.

## HQ-CSW1 Configuration

```bash
spanning-tree vlan 10 root primary
spanning-tree vlan 20 root secondary
```

## HQ-CSW2 Configuration

```bash
spanning-tree vlan 10 root secondary
spanning-tree vlan 20 root primary
```

---

## Part 4

## Verification Commands

```bash
show vlan brief
show interfaces trunk
show etherchannel summary
show standby brief
show spanning-tree vlan 10
show spanning-tree vlan 20
```
---

## Expected Results

- VLANs present on both switches
- EtherChannel up and bundled
- Trunks operational
- HQ-CSW1 active for VLAN 10
- HQ-CSW2 active for VLAN 20
- STP root aligned with HSRP

---

## Key Learning Points

- EtherChannel improves redundancy and bandwidth
- Some IOS versions require manual dot1q encapsulation
- SVIs enable inter-VLAN routing on multilayer switches
- HSRP provides gateway redundancy
- STP tuning improves traffic flow efficiency

---

## Additional Enhancements

To improve the stability, security, and efficiency of the network, several additional configurations were implemented beyond the core requirements.

---

### Restricting VLANs on Trunk Links

Trunk links were configured to only allow the required VLANs instead of all VLANs by default.

```bash
switchport trunk allowed vlan 10,20
```

### Benefits:
- Reduces unnecessary broadcast traffic
- Improves security by limiting VLAN exposure
- Provides better control over Layer 2 traffic

---

## Disabling DTP (Dynamic Trunking Protocol)

DTP was disabled on all manually configured trunk links.

```bash
switchport nonegotiate
```
### Benefits:
- Prevents unwanted trunk negotiation
- Reduces attack surface
- Ensures trunks are explicitly defined

---

## Root Guard (Core Layer)

Root Guard was enabled on interfaces connecting to access switches.

```bash
interface range gi1/0 - 3
 spanning-tree guard root
```
### Purpose:
- Prevents access switches from becoming the STP root
- Ensures root bridge control remains in the core layer

---

## VLAN Design Considerations

Although all trunk links currently allow VLAN 10 and VLAN 20 for simplicity, the design can be further refined by restricting VLANs per access switch based on their role.

For example:

- Admin switches → VLAN 10 only
- Server switches → VLAN 20 only

This would provide:

- Improved segmentation
- Reduced unnecessary traffic
- Stronger security boundaries

---

## Summary of Enhancements

The following improvements were implemented:

- VLAN restriction on trunk links
- Disabled DTP on all trunk interfaces
- Root Guard applied to access-facing ports
- Consideration for further VLAN segmentation

These enhancements align the lab more closely with real-world enterprise network design practices.

---
