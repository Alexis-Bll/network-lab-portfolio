# 02 - Access Layer

## Overview

This phase configures the HQ access layer for the V1 Enterprise Network Lab.

The access layer provides endpoint connectivity for Admin users and server devices at the headquarters site. It operates at Layer 2, with inter-VLAN routing handled by the HQ core switches configured in the previous phase.

This phase focuses on:

- HQ access switch configuration
- VLAN consistency
- Trunk uplinks to the core switches
- Access port configuration for end devices
- Management SVIs for SSH access
- Layer 2 security features
- End-device connectivity testing

---

## Objective

The objective of this phase is to provide reliable and secure access-layer connectivity.

This includes:

- Configuring VLANs on the access switches
- Connecting access switches to both HQ core switches
- Configuring trunk links with allowed VLAN restrictions
- Assigning endpoint-facing ports to the correct VLANs
- Configuring management IP addresses for SSH access
- Enabling Layer 2 security features
- Validating gateway reachability and inter-VLAN routing

---

## Topology Reference

The screenshot below shows the V1 EVE-NG topology.

![V1 EVE-NG Topology](../../topology/v1/v1-eve-ng-topology.png)

The HQ access switches connect end devices into the HQ core.

| Switch | Role | Connected Devices |
|---|---|---|
| HQ-ASW1 | Admin access switch 1 | Admin PC |
| HQ-ASW2 | Admin access switch 2 | Admin PC |
| HQ-SSW1 | Server access switch 1 | Server |
| HQ-SSW2 | Server access switch 2 | Server |

Each access switch has redundant uplinks towards the HQ core switches.

---

## Access Layer Design

The HQ access layer uses four Layer 2 switches:

- HQ-ASW1
- HQ-ASW2
- HQ-SSW1
- HQ-SSW2

The access switches provide:

- End-device connectivity
- VLAN assignment
- Trunk connectivity towards the core
- Layer 2 security enforcement
- In-band management access using VLAN 10

Routing is not performed at the access layer. All Layer 3 routing for HQ VLANs is handled by the core switches using SVIs and HSRP.

---

## VLAN Design

The HQ access layer uses two VLANs:

| VLAN | Name | Purpose |
|---|---|---|
| VLAN 10 | ADMIN | Admin users and switch management |
| VLAN 20 | SERVERS | Server devices |

VLANs were created on the access switches to keep the VLAN database consistent with the HQ core.

```cisco
vlan 10
 name ADMIN

vlan 20
 name SERVERS
```

VLAN 10 is also used for in-band management of the access switches.

---

## Management SVI Design

Although the access switches operate as Layer 2 devices, each switch requires a management IP address for SSH access.

A management SVI is configured on VLAN 10 for each access switch.

| Switch | Management VLAN | Management IP | Default Gateway |
|---|---|---|---|
| HQ-ASW1 | VLAN 10 | 192.168.10.4 | 192.168.10.1 |
| HQ-ASW2 | VLAN 10 | 192.168.10.5 | 192.168.10.1 |
| HQ-SSW1 | VLAN 10 | 192.168.10.6 | 192.168.10.1 |
| HQ-SSW2 | VLAN 10 | 192.168.10.7 | 192.168.10.1 |

The default gateway points to the HSRP virtual IP address for VLAN 10.

```text
192.168.10.1
```

This allows the access switches to remain manageable even if one core switch fails.

---

### HQ-ASW1 Management Configuration

```cisco
interface Vlan10
 ip address 192.168.10.4 255.255.255.0
 no shutdown

ip default-gateway 192.168.10.1
```

---

### HQ-ASW2 Management Configuration

```cisco
interface Vlan10
 ip address 192.168.10.5 255.255.255.0
 no shutdown

ip default-gateway 192.168.10.1
```

---

### HQ-SSW1 Management Configuration

```cisco
interface Vlan10
 ip address 192.168.10.6 255.255.255.0
 no shutdown

ip default-gateway 192.168.10.1
```

---

### HQ-SSW2 Management Configuration

```cisco
interface Vlan10
 ip address 192.168.10.7 255.255.255.0
 no shutdown

ip default-gateway 192.168.10.1
```

---

## Trunk Uplinks to the Core

Each access switch connects back to both HQ core switches using trunk links.

These trunk links allow VLAN 10 and VLAN 20 traffic to pass between the access layer and the core layer.

Example trunk configuration:

```cisco
interface range GigabitEthernet0/0 - 1
 switchport trunk encapsulation dot1q
 switchport mode trunk
 switchport trunk allowed vlan 10,20
 switchport nonegotiate
```

---

## Trunk Hardening

Trunk links were manually configured and restricted to only the VLANs required in the HQ LAN.

| Configuration | Purpose |
|---|---|
| `switchport mode trunk` | Manually sets the interface as a trunk |
| `switchport trunk allowed vlan 10,20` | Restricts the trunk to required VLANs only |
| `switchport nonegotiate` | Disables DTP negotiation |
| `switchport trunk encapsulation dot1q` | Sets 802.1Q trunk encapsulation on the virtual switch image |

This makes trunk behaviour predictable and reduces unnecessary VLAN exposure.

---

## Access Port Configuration

Endpoint-facing interfaces were configured as access ports.

### Admin Access Ports

Admin PCs are connected to VLAN 10.

```cisco
interface GigabitEthernet0/2
 description ADMIN-PC
 switchport mode access
 switchport access vlan 10
 spanning-tree portfast
 spanning-tree bpduguard enable
```

---

### Server Access Ports

Servers are connected to VLAN 20.

```cisco
interface GigabitEthernet0/2
 description SERVER
 switchport mode access
 switchport access vlan 20
 spanning-tree portfast
 spanning-tree bpduguard enable
```

---

## Spanning Tree Access Port Protection

PortFast and BPDU Guard were enabled on endpoint-facing access ports.

| Feature | Purpose |
|---|---|
| PortFast | Allows endpoint ports to move to forwarding state quickly |
| BPDU Guard | Protects against accidental or unauthorised switch connections |

These features are suitable for access ports connected to end devices.

If a BPDU is received on a BPDU Guard-enabled access port, the port is placed into an error-disabled state. This helps protect the Layer 2 topology from accidental loops or rogue switches.

---

## Layer 2 Security Overview

Layer 2 security features were added to improve access-layer protection.

Implemented features include:

- DHCP Snooping
- Dynamic ARP Inspection
- Port Security
- PortFast
- BPDU Guard
- Trunk VLAN restrictions
- DTP disablement

These controls help protect the access layer from common risks such as rogue DHCP servers, ARP spoofing, accidental loops, and unauthorised device changes.

---

## DHCP Snooping

DHCP Snooping was enabled for VLAN 10 and VLAN 20.

```cisco
ip dhcp snooping
ip dhcp snooping vlan 10,20
```

In V1, most endpoints use static IP addressing. DHCP Snooping is still included as part of the Layer 2 security design because it supports Dynamic ARP Inspection and demonstrates how access-layer trust boundaries are configured.

---

### Trusted Interfaces

Trunk uplinks towards the core switches were configured as trusted.

```cisco
interface range GigabitEthernet0/0 - 1
 ip dhcp snooping trust
```

Trusted interfaces are used for infrastructure-facing links.

Endpoint-facing access ports remain untrusted by default.

---

## Dynamic ARP Inspection

Dynamic ARP Inspection was enabled for VLAN 10 and VLAN 20.

```cisco
ip arp inspection vlan 10,20
```

DAI validates ARP traffic and helps protect against ARP spoofing and man-in-the-middle attacks.

---

### DAI Trust Boundary

Infrastructure-facing uplinks towards the core were trusted for ARP inspection.

```cisco
interface range GigabitEthernet0/0 - 1
 ip arp inspection trust
```

Access ports remain untrusted, meaning ARP traffic from end devices is inspected.

---

### Static IP Addressing Consideration

Because the V1 lab uses static IP addressing, static source bindings were added for endpoint devices.

Example:

```cisco
ip source binding 0050.7966.680E vlan 10 192.168.10.10 interface GigabitEthernet0/2
```

These bindings provide the switch with known IP-to-MAC-to-interface information for statically addressed devices.

Without valid bindings, ARP traffic may be dropped and endpoint connectivity can fail.

---

## Static Binding Summary

| Switch | Endpoint | VLAN | IP Address | Interface |
|---|---|---|---|---|
| HQ-ASW1 | Admin PC | VLAN 10 | 192.168.10.10 | Gi0/2 |
| HQ-ASW2 | Admin PC | VLAN 10 | 192.168.10.11 | Gi0/2 |
| HQ-SSW1 | Server | VLAN 20 | 192.168.20.10 | Gi0/2 |
| HQ-SSW2 | Server | VLAN 20 | 192.168.20.11 | Gi0/2 |

---

## Port Security

Port Security was configured on endpoint-facing access ports.

Example configuration:

```cisco
interface GigabitEthernet0/2
 switchport mode access
 switchport access vlan 10
 switchport port-security
 switchport port-security mac-address sticky
 switchport port-security violation restrict
```

---

### Port Security Behaviour

| Setting | Purpose |
|---|---|
| `switchport port-security` | Enables port security on the interface |
| `mac-address sticky` | Learns the connected device MAC address dynamically |
| `violation restrict` | Drops unauthorised traffic but keeps the port operational |

In this lab, Port Security is used to restrict endpoint-facing ports to the expected connected device.

Restrict mode was used so that violations are blocked without immediately shutting down the interface.

---

## End Device IP Addressing

End devices were configured with static IP addresses.

### Admin PCs

| Device | IP Address | Subnet Mask | Default Gateway |
|---|---|---|---|
| Admin PC 1 | 192.168.10.10 | 255.255.255.0 | 192.168.10.1 |
| Admin PC 2 | 192.168.10.11 | 255.255.255.0 | 192.168.10.1 |

---

### Servers

| Device | IP Address | Subnet Mask | Default Gateway |
|---|---|---|---|
| Server 1 | 192.168.20.10 | 255.255.255.0 | 192.168.20.1 |
| Server 2 | 192.168.20.11 | 255.255.255.0 | 192.168.20.1 |

---

## Verification Commands

The following commands were used to verify the access layer configuration.

| Command | Purpose |
|---|---|
| `show vlan brief` | Confirms VLAN creation and access port assignment |
| `show interfaces trunk` | Confirms trunk links and allowed VLANs |
| `show spanning-tree` | Confirms STP operation |
| `show ip interface brief` | Confirms management SVI status |
| `show ip arp inspection statistics` | Verifies DAI operation |
| `show ip arp inspection interfaces` | Confirms trusted and untrusted DAI interfaces |
| `show ip source binding` | Confirms static IP-to-MAC bindings |
| `show port-security interface gi0/2` | Verifies port security status |
| `show port-security address` | Shows secure MAC address entries |

---

## Connectivity Testing

Connectivity testing was performed from endpoint devices to confirm access-layer functionality.

### Gateway Reachability

Admin and server devices were tested against their default gateways.

```cisco
ping 192.168.10.1
ping 192.168.20.1
```

<img width="884" height="668" alt="Gateway reachability test" src="https://github.com/user-attachments/assets/f5888c34-e802-49e4-9bad-9fcfdffa260d" />

---

### Inter-VLAN Routing Test

Connectivity between VLAN 10 and VLAN 20 was tested to confirm that access-layer switching and core-layer inter-VLAN routing were working correctly.

```cisco
ping 192.168.20.10
```

<img width="878" height="239" alt="Inter-VLAN routing test" src="https://github.com/user-attachments/assets/7bc65c49-8915-45de-a271-f9a3b1215ce6" />

---

## Dynamic ARP Inspection Verification

DAI operation was verified using inspection and binding commands.

### ARP Inspection Statistics

```cisco
show ip arp inspection statistics
```

<img width="884" height="412" alt="show ip arp inspection statistics output" src="https://github.com/user-attachments/assets/923e3512-99a9-4153-b6bb-4b8b1284d79c" />

---

### ARP Inspection Interfaces

```cisco
show ip arp inspection interfaces
```

<img width="885" height="338" alt="show ip arp inspection interfaces output" src="https://github.com/user-attachments/assets/132dbdca-1779-4d1a-a4ec-62fee2c0e7e6" />

---

### Source Binding Verification

```cisco
show ip source binding
```

<img width="880" height="197" alt="show ip source binding output" src="https://github.com/user-attachments/assets/130d2439-14fe-496b-8a79-5898cab3d71d" />

---

## Port Security Verification

Port Security was verified on endpoint-facing access ports.

### Interface Port Security

```cisco
show port-security interface gi0/2
```

<img width="882" height="353" alt="show port-security interface output" src="https://github.com/user-attachments/assets/579fd378-3e3e-4af7-9bb5-71edc806b312" />

---

### Secure MAC Address Table

```cisco
show port-security address
```

<img width="887" height="306" alt="show port-security address output" src="https://github.com/user-attachments/assets/fcb71b60-a246-48e4-84e4-3bd6e9303d5c" />

---

## Troubleshooting Note

### Issue Encountered: SVI Down

During testing, gateway reachability initially failed because an SVI was in a down/down state.

The issue was resolved by enabling the SVI:

```cisco
interface Vlan10
 no shutdown
```

An SVI requires:

- The VLAN to exist
- The VLAN to be active on the switch
- At least one active Layer 2 port in that VLAN, or active trunk participation
- The SVI to be administratively enabled

This reinforced the importance of checking both Layer 2 VLAN state and Layer 3 SVI status during troubleshooting.

---

## Expected Results

At the end of this phase:

- VLAN 10 and VLAN 20 are present on the access switches
- Access switches are manageable over VLAN 10
- Access switches use the HSRP virtual gateway for management reachability
- Trunks to the core switches are operational
- Trunks only allow VLAN 10 and VLAN 20
- DTP is disabled on trunk links
- Admin PCs are assigned to VLAN 10
- Servers are assigned to VLAN 20
- Access ports use PortFast and BPDU Guard
- DHCP Snooping and DAI are enabled for VLAN 10 and VLAN 20
- Static bindings support statically addressed endpoints
- Port Security is enabled on endpoint-facing ports
- End devices can reach their default gateways
- Inter-VLAN routing works through the HQ core switches

---

## Design Notes

### Why Access Switches Use VLAN 10 for Management

VLAN 10 is used for switch management because it already exists across the HQ access layer and has routed reachability through the HQ core.

This keeps management simple while still allowing SSH access to each switch.

### Why Access Switches Are Dual-Homed

Each access switch connects to both core switches to provide uplink redundancy.

If one uplink or core switch fails, the access switch still has a path back to the network through the remaining core switch.

### Why Access Ports Use PortFast and BPDU Guard

Endpoint-facing ports do not normally participate in Spanning Tree.

PortFast allows hosts to connect quickly, while BPDU Guard protects the topology if a switch is accidentally connected to an access port.

### Why DAI and DHCP Snooping Were Included

DAI and DHCP Snooping were included to demonstrate common Layer 2 security controls.

Even though V1 uses static addressing, these features show how access-layer trust boundaries can be configured and how ARP protection can be applied.

### Why Port Security Was Used

Port Security adds basic device-level control at the access layer.

It helps prevent unexpected devices from being connected to endpoint-facing switch ports.

---

## Platform Note

This lab was built using virtual Cisco images in EVE-NG.

Some default or platform-generated CLI output may vary between devices. The published configurations and documentation focus on the relevant working configuration and design intent.

---

## Outcome

The HQ access layer was successfully configured to provide:

- VLAN-based endpoint connectivity
- Redundant trunk uplinks to the core
- In-band SSH management access
- Secure access port behaviour
- Layer 2 security controls
- Successful gateway and inter-VLAN connectivity testing

At this stage, the HQ LAN is operational, with access-layer switching provided by the HQ access switches and inter-VLAN routing handled by the HQ core switches.

---

## Key Learning

This phase reinforced several important access-layer concepts:

- Access switches provide endpoint connectivity but do not need to perform routing
- Management SVIs allow Layer 2 switches to be managed remotely
- Trunk links should be manually configured and restricted to required VLANs
- Access ports should use PortFast and BPDU Guard
- DHCP Snooping and DAI help protect against Layer 2 attacks
- Static IP environments require extra care when using DAI
- Port Security can limit access to expected endpoint devices
- Gateway reachability testing is a useful first step when validating access-layer connectivity

---

## Related Documentation

- [Previous: 01 - Core Setup](01-core-setup.md)
- [Next: 03 - Routing with OSPF](03-routing-ospf.md)
- [V1 Overview](./)
- [V1 Topology](../../topology/v1/)
- [Device Configurations](../../configs/)

---
