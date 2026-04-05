# 04 - Branch Switch Configuration

## Objective

The objective of this phase was to configure the branch access switches to provide connectivity for end devices, enforce VLAN segmentation, and apply basic Layer 2 security controls.

Each branch site is designed as a simple access network connected to a branch router, which handles routing and WAN connectivity.

---

## Branch Network Design

Each branch site consists of:

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

```cisco
vlan 30
 name ENGINEERING
```

### BR-SW2 (Sales)

```cisco
vlan 40
 name SALES
```

---

## Management Access (SVI Configuration)

- Branch switches are managed using a Switch Virtual Interface (SVI) within their local VLAN.
- As each branch operates with a single VLAN, the management IP is assigned within the same subnet as the connected end devices. This allows the switch to be accessed remotely via SSH.
- The default gateway is set to the branch router, which provides connectivity to the wider enterprise network.
- Management traffic is carried in-band within the local VLAN, allowing the switch to be reachable from the wider enterprise network via the branch router.

---

### BR-SW1 (Engineering VLAN 30)

```cisco
interface vlan 30
 ip address 192.168.30.2 255.255.255.0
 no shutdown

ip default-gateway 192.168.30.1
```

---

### BR-SW2 (Sales VLAN 40)

```cisco
interface vlan 40
 ip address 192.168.40.2 255.255.255.0
 no shutdown

ip default-gateway 192.168.40.1
```

---

### Design Justification
- Provides simple and effective remote management using SSH
- Avoids introducing additional VLAN complexity at the branch
- Aligns with a single-VLAN branch network design
- Reflects common real-world small branch deployments

---

## Access Port Configuration

Access ports were configured to assign end devices to the correct VLAN.

### BR-SW1

```cisco
interface gi0/1
 description Engineering-PC
 switchport mode access
 switchport access vlan 30
 spanning-tree portfast
 spanning-tree bpduguard enable
```

### BR-SW2

```cisco
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

```cisco
interface gi0/0
 description Uplink to BR1
 switchport mode access
 switchport access vlan 30
```

### BR-SW2

```cisco
interface gi0/0
 description Uplink to BR2
 switchport mode access
 switchport access vlan 40
```

---

## Port Security

Port security was implemented on access ports to restrict each port to a single device.

```cisco
interface gi0/1
 switchport port-security
 switchport port-security maximum 1
 switchport port-security mac-address sticky
 switchport port-security violation restrict
```

---

## Verification

The following commands were used to verify switch operation:


- ```show vlan brief```
<img width="885" height="668" alt="image" src="https://github.com/user-attachments/assets/150da1ad-2d27-409e-8e96-ddd6c4275da9" />

- ```show interfaces status```
<img width="881" height="355" alt="image" src="https://github.com/user-attachments/assets/4d665af2-9452-43e6-8339-078670919d38" />

```show port-security```
<img width="880" height="254" alt="image" src="https://github.com/user-attachments/assets/deb58219-31c7-415b-b228-4f1d0d090e05" />



Connectivity tests were also performed:

- PC to default gateway (branch router)
- PC to HQ VLANs
- PC to remote branch network

<img width="875" height="666" alt="image" src="https://github.com/user-attachments/assets/4e74a9aa-6787-47a5-8d6a-d6fd0f4cfac1" />

These tests confirm both local VLAN connectivity and end-to-end reachability across the enterprise network.

Connectivity across sites confirms correct routing via OSPF configured on the branch and core routers.

Access-control lists have been configured in the next section to stop communication between Sales and Engineering.

---

## Key Design Decisions

- A single VLAN per branch was used to simplify the design
- Routing is handled by the branch router rather than the switch
- Access ports were secured using PortFast, BPDU Guard, and Port Security
- Trunking was not required due to the single VLAN architecture

This reflects a typical small branch deployment in enterprise environments.

---

## Summary

- Branch switches were successfully configured to provide secure Layer 2 connectivity for end devices.
- Each branch site now integrates into the wider enterprise network via its router, allowing full communication with HQ and other branches.
- This completes the branch access layer configuration and prepares the network for implementing traffic control using ACLs.

---
