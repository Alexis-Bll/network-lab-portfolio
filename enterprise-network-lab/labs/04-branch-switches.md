# 04 - Branch Switch Configuration

## Objective

The objective of this phase was to configure the branch access switches to provide connectivity for end devices, enforce VLAN segmentation, and apply basic Layer 2 security controls.

Each branch site is designed as a simple access network connected to a branch router, which handles routing and WAN connectivity.

---

## Branch Network Design

Each branch consists of:

- One access switch
- One router
- One local subnet
- One end device (PC)

Unlike HQ, branch sites use a simplified design with a single VLAN per site.

### Branch VLANs

- Branch 1 (Engineering): 192.168.30.0/24 (VLAN 30)
- Branch 2 (Sales): 192.168.40.0/24 (VLAN 40)

Routing is performed on the branch routers, not the switches.

---

## Spanning Tree Protocol

Rapid PVST+ is implemented across all switches in the network to provide fast Layer 2 convergence.

The full configuration and design details are documented in the Core Layer Setup section:

👉 [View Spanning Tree Configuration](01-core-setup.md)

---

## VLAN Configuration

VLANs were created on each branch switch to match their respective subnet.

### BR-SW1 (Engineering)

```bash
vlan 30
 name ENGINEERING
```

### BR-SW2 (Sales)

```bash
vlan 40
 name SALES
```

---

## Access Port Configuration

Access ports were configured to assign end devices to the correct VLAN.

### BR-SW1

```bash
interface gi0/1
 description Engineering-PC
 switchport mode access
 switchport access vlan 30
 spanning-tree portfast
 spanning-tree bpduguard enable
```

### BR-SW2

```bash
interface gi0/1
 description Sales-PC
 switchport mode access
 switchport access vlan 40
 spanning-tree portfast
 spanning-tree bpduguard enable
```

PortFast was used to allow immediate forwarding, and BPDU Guard was enabled to protect against accidental switch connections.

---

## Uplink Configuration

Each branch switch connects to its local router using an access port.

### BR-SW1

```bash
interface gi0/0
 description Uplink to BR1
 switchport mode access
 switchport access vlan 30
```

### BR-SW2

```bash
interface gi0/0
 description Uplink to BR2
 switchport mode access
 switchport access vlan 40
```

---

## Port Security

Port security was implemented on access ports to restrict each port to a single device.

```bash
interface gi0/1
 switchport port-security
 switchport port-security maximum 1
 switchport port-security mac-address sticky
 switchport port-security violation restrict
```

---

## Verification

The following commands were used to verify switch operation:

```bash
show vlan brief
show interfaces status
show port-security
```

<img width="883" height="666" alt="image" src="https://github.com/user-attachments/assets/383c7eda-529e-496b-86aa-3eb0e31949d5" />


Connectivity tests were also performed:

- PC to default gateway (branch router)
- PC to HQ VLANs
- PC to remote branch network

Successful communication confirmed correct Layer 2 configuration.

---

## Key Design Decisions

- A single VLAN per branch was used to simplify the design
- Routing is handled by the branch router rather than the switch
- Access ports were secured using PortFast, BPDU Guard, and Port Security
- Trunking was not required due to the single VLAN architecture

This reflects a typical small branch deployment in enterprise environments.

---

## Summary

Branch switches were successfully configured to provide secure Layer 2 connectivity for end devices.
Each branch site now integrates into the wider enterprise network via its router, allowing full communication with HQ and other branches.
This completes the branch access layer configuration and prepares the network for implementing traffic control using ACLs.

---
