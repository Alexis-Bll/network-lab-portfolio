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

VLANs were created on both core switches to ensure consistency across the network.

```cisco
vlan 10
 name ADMIN

vlan 20
 name SERVERS
```
---

## EtherChannel Between Core Switches

Two physical interfaces were bundled into a single logical link using LACP.

This provides:
- Increased bandwidth
- Link redundancy
- Simplified management

### Important Platform Note

On this platform, trunk encapsulation must be defined before enabling trunk mode:

```cisco
switchport trunk encapsulation dot1q
```
This behaviour is typical of older Cisco IOS platforms that support multiple encapsulation types.

---

### HQ-CSW1 EtherChannel Configuration

```cisco
interface range gi0/2 - 3
 switchport trunk encapsulation dot1q
 switchport mode trunk
 channel-group 1 mode active

interface port-channel 1
 switchport mode trunk
```

---

### HQ-CSW2 EtherChannel Configuration

```cisco
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

```cisco
interface range gi1/0 - 3
 switchport trunk encapsulation dot1q
 switchport mode trunk
```
Interface numbering may vary depending on the lab topology and switch image used.

---

## Inter-VLAN Routing (SVIs)

Switch Virtual Interfaces (SVIs) were created to provide Layer 3 gateways.

### HQ-CSW1 Configuration

```cisco
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

### HQ-CSW2 Configuration

```cisco
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

## Loopback Interfaces (Management & Router ID)

Loopback interfaces were configured on the core switches to provide a stable management address and consistent Layer 3 identity.

Unlike physical interfaces, loopbacks remain up as long as the device is operational, making them ideal for management access and routing protocols.

---

### HQ-CSW1

```cisco
interface loopback0
 ip address 1.1.1.1 255.255.255.255
```

### HQ-CSW2

```cisco
interface loopback0
 ip address 2.2.2.2 255.255.255.255
```

---

### Integration with Routing and Management

Loopback interfaces are advertised into OSPF to ensure reachability across the network.

They are also used as the source interface for SSH to provide consistent management access.

---

## HSRP Design

HSRP provides redundant default gateways:

| VLAN | Gateway      | Active Switch | Standby Switch |
| ---- | ------------ | ------------- | -------------- |
| 10   | 192.168.10.1 | HQ-CSW1       | HQ-CSW2        |
| 20   | 192.168.20.1 | HQ-CSW2       | HQ-CSW1        |

This design enables load sharing and high availability.

---

## Spanning Tree Protocol (Rapid PVST+)

Rapid Spanning Tree Protocol (Rapid PVST+) was implemented across all switches to provide fast Layer 2 convergence and improve network stability.

Rapid PVST+ significantly reduces convergence time compared to traditional STP, allowing the network to quickly recover from link or device failures in a redundant topology.


### Configuration
```cisco
spanning-tree mode rapid-pvst
```

---

### Design Justification
- Enables faster convergence during topology changes
- Improves failover performance in redundant Layer 2 designs
- Aligns with enterprise best practices for modern switching environments
- Ensures consistent Spanning Tree behaviour across all switches

--- 

### Verification

```cisco
show spanning-tree summary
```

<img width="856" height="580" alt="image" src="https://github.com/user-attachments/assets/e98264f3-7754-4e65-a720-69a618682492" />


Expected output should confirm:

- Spanning Tree mode is Rapid PVST+
- Protocol shown as RSTP

---

## STP Root Placement

Spanning Tree was aligned with HSRP to optimise traffic flow and avoid unnecessary Layer 2 hops.

### HQ-CSW1 Configuration

```cisco
spanning-tree vlan 10 root primary
spanning-tree vlan 20 root secondary
```

### HQ-CSW2 Configuration

```cisco
spanning-tree vlan 10 root secondary
spanning-tree vlan 20 root primary
```

---

## Verification

The following commands were used to validate the configuration:

- ```show vlan brief```
<img width="879" height="236" alt="image" src="https://github.com/user-attachments/assets/d3228a19-7221-4952-9e14-3a6bcefd3c43" />

- ```show interfaces trunk```
<img width="896" height="488" alt="image" src="https://github.com/user-attachments/assets/7fb766d8-ac35-4f44-a7f3-05ae882a801d" />

- ```show etherchannel summary```
<img width="878" height="489" alt="image" src="https://github.com/user-attachments/assets/2f71a724-552e-45c4-a5ff-f4475b4665e1" />

- ```show standby brief```
<img width="875" height="147" alt="image" src="https://github.com/user-attachments/assets/f056fa55-9589-4534-bc89-96ea73c5bcc6" />

- ```show spanning-tree vlan 10```
<img width="883" height="463" alt="image" src="https://github.com/user-attachments/assets/5ad32eae-e969-4054-ace1-c2b96c190ac5" />

- ```show spanning-tree vlan 20```
<img width="882" height="489" alt="image" src="https://github.com/user-attachments/assets/d3330948-b37d-43ba-8e4c-acf7f4e621ea" />

---

## Expected Results
- VLANs present on both switches
- EtherChannel correctly bundled and operational
- Trunk links active
- HSRP active/standby roles aligned with design
- STP root aligned with HSRP

---

## Key Learning Points
- EtherChannel improves both bandwidth and redundancy
- Some IOS platforms require manual 802.1Q encapsulation
- SVIs enable Layer 3 routing on switches
- HSRP provides gateway failover
- STP tuning improves traffic efficiency and convergence

---

## Additional Enhancements

To better reflect real-world enterprise design, the following optimisations were implemented:

### VLAN Restriction on Trunks

Trunk links were configured to only allow the required VLANs instead of all VLANs by default.

```cisco
switchport trunk allowed vlan 10,20
```

Benefits:
- Reduces unnecessary broadcast traffic
- Limits VLAN exposure (security)
- Improves Layer 2 efficiency

---

## Disabling DTP (Dynamic Trunking Protocol)

```cisco
switchport nonegotiate
```

Benefits:
- Prevents unwanted trunk negotiation
- Reduces attack surface
- Ensures manual control of trunking

---

## Root Guard (Access-Facing Ports)

```cisco
interface range gi1/0 - 3
 spanning-tree guard root
```
### Purpose:
- Prevents access switches from becoming STP root
- Maintains control of Layer 2 topology at the core

---

## VLAN Design Considerations

While VLANs 10 and 20 are currently allowed across all trunks, the design can be refined further:
- Admin switches → VLAN 10 only
- Server switches → VLAN 20 only

This would improve:
- Network segmentation
- Traffic efficiency
- Security boundaries

---

## Summary of Enhancements

The core layer provides:
- Centralised inter-VLAN routing
- Redundant default gateways using HSRP
- Optimised Layer 2 forwarding via STP tuning
- High availability through EtherChannel and dual-homing
These configurations establish a resilient and scalable foundation for the network.

---
