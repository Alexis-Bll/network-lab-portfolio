# 01 - Core Setup

## Overview

This phase configures the HQ core layer for the V1 Enterprise Network Lab.

The HQ core is built using two Layer 3 switches:

- HQ-CSW1
- HQ-CSW2

These switches provide the central switching and routing function for the headquarters site.

The core layer is responsible for:

- VLAN creation
- Inter-VLAN routing using SVIs
- Default gateway redundancy using HSRP
- Layer 2 loop prevention using Rapid PVST+
- STP root bridge placement
- EtherChannel between core switches
- Trunk connectivity to access switches
- Routed connectivity towards the HQ WAN routers

---

## Objective

The objective of this phase is to configure a resilient HQ core switching design.

This includes:

- Creating the HQ VLANs
- Configuring SVIs for VLAN gateways
- Enabling Layer 3 routing on the core switches
- Configuring HSRP for gateway redundancy
- Configuring Rapid PVST+ for Layer 2 stability
- Aligning STP root bridge placement with HSRP active gateways
- Configuring LACP EtherChannel between the core switches
- Hardening trunk links using allowed VLAN lists and DTP disablement
- Preparing routed uplinks towards the HQ WAN edge

---

## Topology Reference

The screenshot below shows the V1 EVE-NG topology.

![V1 EVE-NG Topology](../../topology/v1/v1-eve-ng-topology.png)

The HQ core switches sit at the centre of the headquarters network and connect to:

- HQ access switches
- HQ server switches
- HQ routers
- Each other through an EtherChannel trunk

---

## Core Layer Design

The HQ core uses a collapsed core/access design.

This keeps the lab realistic for a small enterprise environment while remaining suitable for a CCNA-level project.

| Device | Role |
|---|---|
| HQ-CSW1 | Core switch, preferred HSRP active gateway for VLAN 10 |
| HQ-CSW2 | Core switch, preferred HSRP active gateway for VLAN 20 |

This design allows both core switches to actively forward traffic instead of having one switch remain mostly idle.

---

## VLAN Design

The HQ core switches support the following VLANs:

| VLAN | Name | Purpose |
|---|---|---|
| 10 | ADMIN | Admin user network |
| 20 | SERVERS | Server network |

VLANs were created on both core switches to keep the Layer 2 database consistent.

```cisco
vlan 10
 name ADMIN

vlan 20
 name SERVERS
```

Branch VLANs are not extended across the HQ switching fabric in V1. Engineering and Sales are routed branch networks connected through the WAN.

---

## Inter-VLAN Routing

Inter-VLAN routing is provided by SVIs on the HQ core switches.

Layer 3 routing is enabled using:

```cisco
ip routing
```

This allows the core switches to route traffic between VLAN 10, VLAN 20, and the routed WAN links.

---

## SVI and HSRP Design

HSRP provides a resilient virtual default gateway for each HQ VLAN.

| VLAN | Virtual Gateway | HQ-CSW1 IP | HQ-CSW2 IP | Preferred Active Device |
|---|---|---|---|---|
| VLAN 10 | 192.168.10.1 | 192.168.10.2 | 192.168.10.3 | HQ-CSW1 |
| VLAN 20 | 192.168.20.1 | 192.168.20.2 | 192.168.20.3 | HQ-CSW2 |

This provides gateway redundancy and basic load sharing across both core switches.

---

### HQ-CSW1 SVI Configuration

```cisco
interface Vlan10
 ip address 192.168.10.2 255.255.255.0
 standby 10 ip 192.168.10.1
 standby 10 priority 110
 standby 10 preempt
 no shutdown

interface Vlan20
 ip address 192.168.20.2 255.255.255.0
 standby 20 ip 192.168.20.1
 standby 20 priority 90
 standby 20 preempt
 no shutdown
```

HQ-CSW1 has a higher HSRP priority for VLAN 10, making it the preferred active gateway for the Admin VLAN.

---

### HQ-CSW2 SVI Configuration

```cisco
interface Vlan10
 ip address 192.168.10.3 255.255.255.0
 standby 10 ip 192.168.10.1
 standby 10 priority 90
 standby 10 preempt
 no shutdown

interface Vlan20
 ip address 192.168.20.3 255.255.255.0
 standby 20 ip 192.168.20.1
 standby 20 priority 110
 standby 20 preempt
 no shutdown
```

HQ-CSW2 has a higher HSRP priority for VLAN 20, making it the preferred active gateway for the Server VLAN.

---

## Loopback Interfaces

Loopback interfaces are configured on both core switches to provide stable Layer 3 identities.

| Device | Loopback |
|---|---|
| HQ-CSW1 | 1.1.1.1/32 |
| HQ-CSW2 | 2.2.2.2/32 |

### HQ-CSW1

```cisco
interface Loopback0
 ip address 1.1.1.1 255.255.255.255
```

### HQ-CSW2

```cisco
interface Loopback0
 ip address 2.2.2.2 255.255.255.255
```

The loopbacks are later advertised into OSPF and used for consistent device identification during routing and testing.

---

## EtherChannel Between Core Switches

An LACP EtherChannel is configured between HQ-CSW1 and HQ-CSW2.

This bundles two physical links into one logical Port-Channel.

Benefits:

- Increased bandwidth between core switches
- Link redundancy
- Simplified Spanning Tree behaviour
- A single logical trunk between both core devices

---

### Platform Note

The Cisco virtual switch image used in this lab requires trunk encapsulation to be configured before enabling trunk mode.

```cisco
switchport trunk encapsulation dot1q
```

This is common on older Cisco IOS switching platforms that support multiple trunk encapsulation types.

---

### HQ-CSW1 EtherChannel Configuration

```cisco
interface range GigabitEthernet0/2 - 3
 switchport trunk encapsulation dot1q
 switchport mode trunk
 switchport trunk allowed vlan 10,20
 switchport nonegotiate
 channel-group 1 mode active

interface Port-channel1
 switchport trunk encapsulation dot1q
 switchport mode trunk
 switchport trunk allowed vlan 10,20
 switchport nonegotiate
```

---

### HQ-CSW2 EtherChannel Configuration

```cisco
interface range GigabitEthernet0/2 - 3
 switchport trunk encapsulation dot1q
 switchport mode trunk
 switchport trunk allowed vlan 10,20
 switchport nonegotiate
 channel-group 1 mode active

interface Port-channel1
 switchport trunk encapsulation dot1q
 switchport mode trunk
 switchport trunk allowed vlan 10,20
 switchport nonegotiate
```

---

## Trunk Links to Access Switches

The HQ access switches are dual-homed to both core switches.

These uplinks are configured as 802.1Q trunks so VLAN traffic can pass between the access layer and core layer.

Example trunk configuration:

```cisco
interface GigabitEthernet1/0
 switchport trunk encapsulation dot1q
 switchport mode trunk
 switchport trunk allowed vlan 10,20
 switchport nonegotiate
 spanning-tree guard root
```

### Trunk Hardening

The trunk links include two hardening measures.

| Feature | Purpose |
|---|---|
| `switchport trunk allowed vlan 10,20` | Restricts the trunk to only the required VLANs |
| `switchport nonegotiate` | Disables DTP and prevents dynamic trunk negotiation |

This improves security and makes trunk behaviour predictable.

---

## Root Guard on Access-Facing Trunks

Root Guard is applied on core switch interfaces facing the access layer.

```cisco
spanning-tree guard root
```

This helps prevent an access switch from becoming the STP root bridge.

The purpose is to keep STP control at the core layer, where the intended root bridge placement has been configured.

---

## Routed Uplinks to HQ Routers

The core switches also connect to the HQ WAN routers using routed Layer 3 links.

These links use `/30` point-to-point networks from the `10.0.1.0/24` range.

Example routed interface:

```cisco
interface GigabitEthernet0/0
 description TO HQ-R1
 no switchport
 ip address 10.0.1.1 255.255.255.252
 no shutdown
```

These routed links are used later in the OSPF phase to provide connectivity from the HQ core towards the WAN and branch networks.

---

## Spanning Tree Protocol

Rapid PVST+ is configured across the switching environment.

```cisco
spanning-tree mode rapid-pvst
```

Rapid PVST+ provides faster convergence than traditional STP and is suitable for redundant Layer 2 designs.

---

## STP Root Bridge Design

STP root bridge placement is aligned with the HSRP active gateway design.

| VLAN | HSRP Active Gateway | STP Root Bridge |
|---|---|---|
| VLAN 10 | HQ-CSW1 | HQ-CSW1 |
| VLAN 20 | HQ-CSW2 | HQ-CSW2 |

This keeps Layer 2 forwarding paths aligned with the preferred Layer 3 gateway.

---

### HQ-CSW1 STP Configuration

```cisco
spanning-tree vlan 10 priority 24576
spanning-tree vlan 20 priority 28672
```

HQ-CSW1 is preferred as the STP root for VLAN 10 and secondary for VLAN 20.

---

### HQ-CSW2 STP Configuration

```cisco
spanning-tree vlan 10 priority 28672
spanning-tree vlan 20 priority 24576
```

HQ-CSW2 is preferred as the STP root for VLAN 20 and secondary for VLAN 10.

---

## Verification Commands

The following commands were used to verify the core setup.

| Command | Purpose |
|---|---|
| `show vlan brief` | Confirms VLAN creation |
| `show interfaces trunk` | Confirms trunk links and allowed VLANs |
| `show etherchannel summary` | Confirms EtherChannel status |
| `show standby brief` | Confirms HSRP active and standby roles |
| `show spanning-tree summary` | Confirms Rapid PVST+ operation |
| `show spanning-tree vlan 10` | Confirms STP root placement for VLAN 10 |
| `show spanning-tree vlan 20` | Confirms STP root placement for VLAN 20 |
| `show ip interface brief` | Confirms SVI and routed interface status |

---

## Verification Evidence

### VLAN Verification

`show vlan brief` was used to confirm that VLANs were created.

<img width="879" height="236" alt="show vlan brief output" src="https://github.com/user-attachments/assets/d3228a19-7221-4952-9e14-3a6bcefd3c43" />

---

### Trunk Verification

`show interfaces trunk` was used to confirm trunk operation and allowed VLANs.

<img width="896" height="488" alt="show interfaces trunk output" src="https://github.com/user-attachments/assets/7fb766d8-ac35-4f44-a7f3-05ae882a801d" />

---

### EtherChannel Verification

`show etherchannel summary` was used to confirm that the physical links were bundled into Port-Channel1.

<img width="878" height="489" alt="show etherchannel summary output" src="https://github.com/user-attachments/assets/2f71a724-552e-45c4-a5ff-f4475b4665e1" />

---

### HSRP Verification

`show standby brief` was used to confirm HSRP active and standby roles.

<img width="875" height="147" alt="show standby brief output" src="https://github.com/user-attachments/assets/f056fa55-9589-4534-bc89-96ea73c5bcc6" />

---

### Rapid PVST+ Verification

`show spanning-tree summary` confirmed Rapid PVST+ operation.

<img width="874" height="423" alt="show spanning-tree summary output" src="https://github.com/user-attachments/assets/e6bf65a4-1e50-4481-9b0b-d38b4e0d27d4" />

---

### VLAN 10 STP Verification

`show spanning-tree vlan 10` was used to confirm the STP state for VLAN 10.

<img width="883" height="463" alt="show spanning-tree vlan 10 output" src="https://github.com/user-attachments/assets/5ad32eae-e969-4054-ace1-c2b96c190ac5" />

---

### VLAN 20 STP Verification

`show spanning-tree vlan 20` was used to confirm the STP state for VLAN 20.

<img width="882" height="489" alt="show spanning-tree vlan 20 output" src="https://github.com/user-attachments/assets/d3330948-b37d-43ba-8e4c-acf7f4e621ea" />

---

## Expected Results

At the end of this phase:

- VLAN 10 and VLAN 20 exist on both core switches
- SVIs are configured for Admin and Server VLANs
- HSRP virtual gateways are available
- HQ-CSW1 is preferred active for VLAN 10
- HQ-CSW2 is preferred active for VLAN 20
- Rapid PVST+ is enabled
- STP root bridge placement aligns with HSRP active gateways
- Port-Channel1 is operational between HQ-CSW1 and HQ-CSW2
- Trunk links only allow the required VLANs
- DTP is disabled on trunk links
- Routed uplinks towards HQ routers are prepared for OSPF

---

## Design Notes

### Why HSRP Was Used

HSRP provides a virtual default gateway for each VLAN.

If one core switch fails, hosts can continue using the same default gateway IP address while the standby switch takes over.

### Why STP Was Aligned with HSRP

STP was aligned with HSRP so that the preferred Layer 2 forwarding path matches the preferred Layer 3 gateway.

This avoids inefficient traffic flow where hosts forward traffic towards one switch, only for the traffic to cross to the other switch to reach the active gateway.

### Why EtherChannel Was Used

EtherChannel allows multiple physical links to operate as a single logical link.

This increases bandwidth and provides link redundancy while keeping the topology simpler for STP.

### Why Trunks Were Restricted

Trunks were restricted to VLANs 10 and 20 because only those VLANs are required across the HQ switching infrastructure in V1.

Restricting VLANs reduces unnecessary VLAN exposure and improves clarity during troubleshooting.

---

## Platform Note

This lab was built using virtual Cisco images in EVE-NG.

Some default or platform-generated CLI output may vary between devices. The published configurations and documentation focus on the relevant working configuration and design intent.

---

## Outcome

The HQ core layer was successfully configured to provide:

- VLAN segmentation
- Inter-VLAN routing
- HSRP gateway redundancy
- STP-controlled Layer 2 redundancy
- EtherChannel between core switches
- Hardened trunk links
- Routed links towards the WAN edge

This created the resilient HQ network foundation required before configuring the access layer, WAN routing, ACLs, and final validation.

---

## Key Learning

This phase reinforced several important enterprise networking concepts:

- Layer 3 switches can provide inter-VLAN routing using SVIs
- HSRP improves gateway availability
- STP root placement should align with the intended traffic path
- EtherChannel improves bandwidth and redundancy between switches
- Trunk links should be manually configured and restricted to required VLANs
- DTP should be disabled where dynamic trunk negotiation is not required
- Root Guard helps protect the intended STP root bridge design

---

## Related Documentation

- [Previous: 00 - Base Configuration](00-base-config.md)
- [Next: 02 - Access Layer](02-access-layer.md)
- [V1 Overview](./)
- [V1 Topology](../../topology/v1/)
- [Device Configurations](../../configs/)

---
