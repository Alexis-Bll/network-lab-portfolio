# DHCP Services

This section documents the implementation of centralised DHCP services for the enterprise lab. A Windows Server 2008 R2 DHCP server was deployed in the HQ server VLAN to provide addressing for the Admin, Engineering, and Sales networks across the topology.

This lab builds on a previously secured Layer 2 environment (V1), where DHCP Snooping and Dynamic ARP Inspection (DAI) were already implemented. Introducing DHCP services in V2 required careful integration to avoid breaking existing security controls.

---

## Objective

The goal was to:

- Centralise DHCP services on a dedicated server at HQ  
- Provide dynamic addressing for multiple VLANs across the network  
- Use DHCP relay (`ip helper-address`) to forward broadcasts across Layer 3  
- Validate end-to-end connectivity between dynamically addressed hosts  
- Integrate DHCP with existing Layer 2 security (DHCP Snooping & DAI)
- Troubleshoot and resolve conflicts between DHCP services and security features

---

## DHCP Server

The DHCP server is hosted on **DC1** in the HQ server VLAN.

### Server Details

- **Hostname:** DC1  
- **IP Address:** `192.168.20.10`  
- **Subnet:** `255.255.255.0`  
- **Default Gateway:** `192.168.20.1`  

### DHCP Scopes

The following scopes were configured and activated:

- `192.168.10.0/24` → **Admin VLAN**  
- `192.168.30.0/24` → **Engineering VLAN**  
- `192.168.40.0/24` → **Sales VLAN**  

Each scope includes the correct default gateway for its VLAN.

---

<img width="1089" height="812" alt="image" src="https://github.com/user-attachments/assets/353c7e1b-ad51-4009-b4b1-3b7eb43e0cde" />

---

<img width="1085" height="812" alt="image" src="https://github.com/user-attachments/assets/01a9316d-6810-4cfa-a26e-a1fb370acf3e" />

---

## DHCP Relay Configuration

Because the DHCP server is located in a different VLAN, DHCP relay was required.

### Example Configuration

```cisco
interface Vlan10
 ip address 192.168.10.2 255.255.255.0
 ip helper-address 192.168.20.10
 standby 10 ip 192.168.10.1
```

Relay was configured on all Layer 3 gateways serving client VLANs.

---

## Expected Behaviour

This section outlines the expected behaviour after DHCP configuration.

- Clients should receive DHCP addresses dynamically
- Inter-VLAN routing should function correctly
- The DHCP server should be reachable from all VLANs

However, this expected behaviour was not initially observed due to existing Layer 2 security configurations, which required further troubleshooting.

### Example DHCP Assignments
- Admin: 192.168.10.50/24
- Engineering: 192.168.30.50/24
- Sales: 192.168.40.50/24

---

## Troubleshooting Process

After implementing DHCP services, clients were unable to obtain IP addresses despite correct DHCP server and relay configuration.

Through step-by-step debugging, the following observations were made:

- Static IP addressing worked correctly
- Inter-VLAN routing was functional
- DHCP server was reachable from all VLANs
- Only DHCP requests were failing

This indicated the issue was not related to Layer 3 routing, but rather a Layer 2 control-plane problem.

Further investigation identified DHCP Snooping as the root cause, as it was blocking legitimate DHCP traffic due to incorrect trust boundaries and Option 82 behaviour.

---

## DHCP Snooping Implementation

DHCP Snooping was already implemented in the network from V1 as a Layer 2 security feature.

When DHCP services were introduced in V2, DHCP Snooping initially prevented clients from receiving IP addresses, requiring troubleshooting and refinement of the existing configuration.

### Design Principles

- DHCP Snooping must align with actual traffic flow
- Only uplinks and DHCP server paths should be trusted
- Client access ports must remain untrusted
- Option 82 may need to be disabled when using DHCP relay

---

### Example Configuration (Access Switch)

```cisco
ip dhcp snooping
ip dhcp snooping vlan 10,20
no ip dhcp snooping information option

interface GigabitEthernet0/0
 ip dhcp snooping trust

interface GigabitEthernet0/1
 ip dhcp snooping trust
```

---

### Example Configuration (Server Switch)

```cisco
ip dhcp snooping
ip dhcp snooping vlan 10,20
no ip dhcp snooping information option

interface GigabitEthernet0/0
 ip dhcp snooping trust

interface GigabitEthernet0/1
 ip dhcp snooping trust

interface GigabitEthernet0/2
 ip dhcp snooping trust
```

---

### Example Configuration (Branch Switch)

```cisco
ip dhcp snooping
ip dhcp snooping vlan 30
no ip dhcp snooping information option

interface GigabitEthernet0/0
 ip dhcp snooping trust
```

---

## DHCP Snooping Troubleshooting

### Symptoms
- Clients failed to obtain IP addresses
- `Can't find dhcp server` observed on VPCS
- Static IP connectivity worked fine

---

## Root Cause

DHCP failed due to an interaction between existing Layer 2 security controls and DHCP relay:

- DHCP Snooping trust boundaries were incorrectly applied
- Legitimate DHCP responses were being dropped on untrusted paths
- Option 82 (relay information) insertion caused incompatibility with the DHCP server

---

## Fix

The working solution:

```cisco
no ip dhcp snooping information option
```

AND:

- Trust only uplinks + DHCP server path
- Keep client ports untrusted

---

## Key Lesson

DHCP Snooping requires careful design:

- Trust must match actual traffic flow
- Relay + Option 82 can break DHCP if not handled properly
- Security features should be introduced incrementally

---

## Dynamic ARP Inspection (DAI)

DAI was already present from V1 and relies on the DHCP Snooping binding table to validate ARP packets.

After DHCP Snooping was fixed, DAI was validated to ensure it did not block legitimate traffic.

### Notes
- DAI depends on a valid DHCP Snooping binding table
- Incorrect DHCP Snooping configuration will cause DAI to drop legitimate ARP traffic
- Enabled only on VLANs present on each switch
- Uplinks must be trusted

---

### Example Configuration

```cisco
ip arp inspection vlan 30

interface GigabitEthernet0/0
 ip arp inspection trust
```

---

## Validation

The following tests were performed:
- DHCP assignment across all VLANs
- Ping to default gateways
- Inter-VLAN communication
- Connectivity to DHCP server (192.168.20.10)
- DHCP Snooping functionality verified
- DAI not blocking legitimate traffic

---

## Outcome

The network now supports:

- Centralised DHCP across multiple VLANs
- DHCP relay across Layer 3 boundaries
- DHCP Snooping protection
- Dynamic ARP Inspection (DAI) security
- Full connectivity between dynamically assigned clients

## Key Takeaway

This lab demonstrates a critical real-world networking concept:

Layer 2 security features such as DHCP Snooping and DAI can unintentionally disrupt core services if not carefully designed.

The key is understanding:

- Traffic flow across the network
- Trust boundaries
- How security features interact with protocols like DHCP

This troubleshooting process reflects real enterprise network challenges, where security and functionality must be balanced correctly.

---
