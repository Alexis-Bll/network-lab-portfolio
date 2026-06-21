# 04 - Branch Switches

## Overview

This phase configures the branch access switches for the V1 Enterprise Network Lab.

Each branch site uses a simple Layer 2 access switch connected to a local branch router. The branch router provides the default gateway for the local VLAN and handles routing towards the wider enterprise WAN.

This phase focuses on:

- Branch VLAN creation
- Branch switch management access
- Access port configuration
- Router uplink configuration
- PortFast and BPDU Guard
- Port Security
- Local and remote connectivity testing

---

## Objective

The objective of this phase is to configure the branch switches so that Engineering and Sales endpoints can connect securely to the enterprise network.

This includes:

- Creating the correct branch VLAN on each switch
- Assigning management IP addresses using SVIs
- Setting the branch router as the default gateway
- Configuring endpoint-facing access ports
- Configuring router-facing access ports
- Applying basic access-layer security controls
- Validating connectivity from each branch to HQ and other routed networks

---

## Topology Reference

The screenshot below shows the V1 EVE-NG topology.

![V1 EVE-NG Topology](../../topology/v1/v1-eve-ng-topology.png)

The branch switches are located at the Engineering and Sales branch sites.

| Branch | Router | Switch | VLAN | Subnet |
|---|---|---|---|---|
| Branch 1 | BR1 | BR-SW1 | VLAN 30 | 192.168.30.0/24 |
| Branch 2 | BR2 | BR-SW2 | VLAN 40 | 192.168.40.0/24 |

---

## Branch Network Design

Each branch site uses a simple small-office design.

Each branch contains:

- One branch router
- One Layer 2 access switch
- One local VLAN
- One endpoint PC

Unlike the HQ site, the branch switches do not perform routing. Routing is handled by the branch routers.

| Site | VLAN | Purpose | Default Gateway |
|---|---|---|---|
| Branch 1 | VLAN 30 | Engineering | 192.168.30.1 via BR1 |
| Branch 2 | VLAN 40 | Sales | 192.168.40.1 via BR2 |

This keeps the branch design simple and reflects a typical small branch deployment.

---

## Branch Switch Roles

| Device | Role |
|---|---|
| BR-SW1 | Engineering branch access switch |
| BR-SW2 | Sales branch access switch |

The branch switches provide:

- Local endpoint connectivity
- VLAN assignment
- In-band management access
- Layer 2 access port security
- Connectivity to the branch router

---

## VLAN Configuration

Each branch switch has one local VLAN.

### BR-SW1 - Engineering

```cisco
vlan 30
 name ENGINEERING
```

### BR-SW2 - Sales

```cisco
vlan 40
 name SALES
```

Only the required local VLAN is configured on each branch switch.

---

## Management SVI Configuration

Each branch switch uses an SVI for in-band management.

Because each branch only has one VLAN, the switch management IP is placed in the same subnet as the local branch endpoint.

| Switch | Management VLAN | Management IP | Default Gateway |
|---|---|---|---|
| BR-SW1 | VLAN 30 | 192.168.30.2 | 192.168.30.1 |
| BR-SW2 | VLAN 40 | 192.168.40.2 | 192.168.40.1 |

The default gateway is the local branch router.

---

### BR-SW1 Management Configuration

```cisco
interface Vlan30
 ip address 192.168.30.2 255.255.255.0
 no shutdown

ip default-gateway 192.168.30.1
```

---

### BR-SW2 Management Configuration

```cisco
interface Vlan40
 ip address 192.168.40.2 255.255.255.0
 no shutdown

ip default-gateway 192.168.40.1
```

---

## Router Uplink Configuration

Each branch switch connects to its local router using an access port.

A trunk is not required because each branch uses a single VLAN.

### BR-SW1 Uplink to BR1

```cisco
interface GigabitEthernet0/0
 description Uplink to BR1
 switchport mode access
 switchport access vlan 30
```

---

### BR-SW2 Uplink to BR2

```cisco
interface GigabitEthernet0/0
 description Uplink to BR2
 switchport mode access
 switchport access vlan 40
```

The branch router interface connected to the switch acts as the default gateway for the local branch subnet.

---

## Endpoint Access Port Configuration

Endpoint-facing interfaces were configured as access ports in the correct VLAN.

### BR-SW1 - Engineering PC

```cisco
interface GigabitEthernet0/1
 description ENGINEERING-PC
 switchport mode access
 switchport access vlan 30
 spanning-tree portfast
 spanning-tree bpduguard enable
```

---

### BR-SW2 - Sales PC

```cisco
interface GigabitEthernet0/1
 description SALES-PC
 switchport mode access
 switchport access vlan 40
 spanning-tree portfast
 spanning-tree bpduguard enable
```

---

## Spanning Tree Configuration

Rapid PVST+ is enabled on the branch switches.

```cisco
spanning-tree mode rapid-pvst
```

The branch switches are simple access switches with no redundant Layer 2 paths in this section of the topology.

PortFast and BPDU Guard are applied to endpoint-facing ports.

| Feature | Purpose |
|---|---|
| PortFast | Allows endpoint ports to move to forwarding state quickly |
| BPDU Guard | Protects against accidental or unauthorised switch connections |

If a BPDU is received on an endpoint-facing port, BPDU Guard can place the port into an error-disabled state to protect the Layer 2 topology.

---

## Port Security

Port Security was configured on endpoint-facing access ports.

Example:

```cisco
interface GigabitEthernet0/1
 switchport port-security
 switchport port-security mac-address sticky
 switchport port-security violation restrict
```

Port Security helps restrict the access port to the expected connected endpoint.

---

### Port Security Behaviour

| Setting | Purpose |
|---|---|
| `switchport port-security` | Enables Port Security on the interface |
| `mac-address sticky` | Dynamically learns the connected device MAC address |
| `violation restrict` | Drops unauthorised traffic while keeping the port operational |

Restrict mode was used so that violations are blocked without immediately shutting down the interface.

---

## End Device IP Addressing

Branch endpoints were configured with static IP addresses.

| Device | VLAN | IP Address | Subnet Mask | Default Gateway |
|---|---|---|---|---|
| Engineering PC | VLAN 30 | 192.168.30.10 | 255.255.255.0 | 192.168.30.1 |
| Sales PC | VLAN 40 | 192.168.40.10 | 255.255.255.0 | 192.168.40.1 |

The default gateway for each endpoint is the local branch router.

---

## Branch Router Role

The branch routers provide Layer 3 connectivity for each branch.

| Router | LAN Interface | LAN IP | WAN Role |
|---|---|---|---|
| BR1 | Engineering LAN | 192.168.30.1/24 | Connects Branch 1 to the ISP/WAN router |
| BR2 | Sales LAN | 192.168.40.1/24 | Connects Branch 2 to the ISP/WAN router |

The routers participate in OSPF, which allows branch networks to be advertised across the enterprise WAN.

The branch switches themselves do not run OSPF.

---

## Verification Commands

The following commands were used to verify the branch switch configuration.

| Command | Purpose |
|---|---|
| `show vlan brief` | Confirms VLAN creation and port assignment |
| `show interfaces status` | Checks interface status and VLAN membership |
| `show port-security` | Confirms Port Security status |
| `show port-security interface gi0/1` | Verifies Port Security behaviour on the endpoint port |
| `show ip interface brief` | Confirms management SVI status |
| `show running-config` | Reviews the configured switch settings |

---

## Verification Evidence

### VLAN Verification

`show vlan brief` was used to confirm the branch VLAN and access port assignment.

<img width="885" height="668" alt="show vlan brief output" src="https://github.com/user-attachments/assets/150da1ad-2d27-409e-8e96-ddd6c4275da9" />

---

### Interface Status Verification

`show interfaces status` was used to confirm the operational state of the branch switch ports.

<img width="881" height="355" alt="show interfaces status output" src="https://github.com/user-attachments/assets/4d665af2-9452-43e6-8339-078670919d38" />

---

### Port Security Verification

`show port-security` was used to confirm Port Security status on the branch switch.

<img width="880" height="254" alt="show port-security output" src="https://github.com/user-attachments/assets/deb58219-31c7-415b-b228-4f1d0d090e05" />

---

## Connectivity Testing

Connectivity testing was performed from branch endpoints to confirm that local switching and routed WAN connectivity were working correctly.

Tests included:

- Branch PC to local default gateway
- Branch PC to HQ VLANs
- Branch PC to the remote branch network

Example tests:

```cisco
ping 192.168.30.1
ping 192.168.10.1
ping 192.168.20.10
ping 192.168.40.10
```

<img width="875" height="666" alt="branch connectivity testing output" src="https://github.com/user-attachments/assets/4e74a9aa-6787-47a5-8d6a-d6fd0f4cfac1" />

These tests confirm that the branch switches provide local Layer 2 connectivity and that OSPF routing through the branch routers provides wider enterprise reachability.

Access Control Lists are configured in the next phase to restrict direct communication between the Engineering and Sales branch networks.

---

## Expected Results

At the end of this phase:

- BR-SW1 has VLAN 30 configured for Engineering
- BR-SW2 has VLAN 40 configured for Sales
- Branch switches have management SVIs
- Branch switches use the local branch router as their default gateway
- Router uplinks are configured as access ports
- Endpoint ports are assigned to the correct VLAN
- PortFast and BPDU Guard are enabled on endpoint-facing ports
- Port Security is enabled on endpoint-facing ports
- Branch PCs can reach their local default gateways
- Branch PCs can reach required HQ networks through OSPF routing

---

## Design Notes

### Why Each Branch Uses a Single VLAN

Each branch is designed as a small office with one department.

Using a single VLAN per branch keeps the topology simple and avoids unnecessary complexity.

### Why the Router Provides the Default Gateway

The branch router acts as both the LAN default gateway and the WAN edge device.

This is a common small-site design where a local router connects the branch LAN to the wider enterprise network.

### Why the Router Uplink Is an Access Port

A trunk is not required between the branch switch and branch router because each branch only uses one VLAN.

The router-facing switch port is placed directly into the local branch VLAN.

### Why the Branch Switches Do Not Run OSPF

The branch switches operate at Layer 2.

They only provide local switching, endpoint access, and management reachability.

OSPF runs on the branch routers because they are responsible for routing between the local LAN and the WAN.

### Why Port Security Was Used

Port Security was used to add basic endpoint control at the access layer.

It helps limit the endpoint-facing port to the expected connected device.

---

## Platform Note

This lab was built using virtual Cisco images in EVE-NG.

Some default or platform-generated CLI output may vary between devices. The published configurations and documentation focus on the relevant working configuration and design intent.

---

## Outcome

The branch switches were successfully configured to provide:

- Local VLAN-based endpoint connectivity
- In-band management access
- Access port security
- Connectivity to the local branch router
- End-to-end reachability across the enterprise network

At this stage, the Engineering and Sales branches are connected into the wider V1 enterprise network through their branch routers.

---

## Key Learning

This phase reinforced several important branch networking concepts:

- Small branches can use a simple router-and-switch design
- A single VLAN is suitable for a small branch with one department
- Layer 2 switches can be managed using an SVI and default gateway
- Router-facing switch ports can use access mode when only one VLAN is required
- PortFast and BPDU Guard should be used on endpoint-facing ports
- Port Security provides basic protection against unexpected endpoint changes
- Branch routers should handle routing when switches are operating at Layer 2

---

## Related Documentation

- [Previous: 03 - Routing with OSPF](03-routing-ospf.md)
- [Next: 05 - Access Control Lists](05-access-control-lists.md)
- [V1 Overview](./)
- [V1 Topology](../../topology/v1/)
- [Device Configurations](../../configs/)

---
