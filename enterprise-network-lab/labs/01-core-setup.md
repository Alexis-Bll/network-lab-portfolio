# 01 - Core Layer Setup

## Objective

Configure the HQ core layer to provide VLAN segmentation, Layer 3 gateway redundancy, and resilient switching between the core and access layers.

This phase focuses on the two core switches:

- HQ-CSW1
- HQ-CSW2

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

HQ-CSW1 was configured as the preferred device for VLAN 10, while HQ-CSW2 was configured as the preferred device for VLAN 20. This allows traffic load-sharing across the two core switches instead of leaving one mostly idle.

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

---

## Verification Commands

```bash
show vlan brief
show interfaces trunk
show etherchannel summary
show standby brief
show spanning-tree vlan 10
show spanning-tree vlan 20
```

## Expected Results

- VLANs present on both switches
- EtherChannel up and bundled
- Trunks operational
- CSW1 active for VLAN 10
- CSW2 active for VLAN 20
- STP root aligned with HSRP

## Key Learning Points

- EtherChannel improves redundancy and bandwidth
- Some IOS versions require manual dot1q encapsulation
- SVIs enable inter-VLAN routing on multilayer switches
- HSRP provides gateway redundancy
- STP tuning improves traffic flow efficiency

