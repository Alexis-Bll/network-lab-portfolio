# DHCP Services

This section documents the implementation of centralised DHCP services for the enterprise lab. A Windows Server 2008 R2 DHCP server was deployed in the HQ server VLAN to provide addressing for the Admin, Engineering, and Sales networks across the topology.

---

## Objective

The goal was to:

- Centralise DHCP services on a dedicated server at HQ  
- Provide dynamic addressing for multiple VLANs across the network  
- Use DHCP relay (`ip helper-address`) to forward broadcasts across Layer 3  
- Validate end-to-end connectivity between dynamically addressed hosts  
- Introduce Layer 2 security (DHCP Snooping & DAI)  

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

<img width="1089" height="812" alt="image" src="https://github.com/user-attachments/assets/353c7e1b-ad51-4009-b4b1-3b7eb43e0cde" />

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

## Initial Verification

After configuration:
- Clients successfully received DHCP addresses
- Inter-VLAN routing worked correctly
- DHCP server reachable from all VLANs

Example DHCP Assignments
- Admin: 192.168.10.50/24
- Engineering: 192.168.30.50/24
- Sales: 192.168.40.50/24

## DHCP Snooping Implementation

DHCP Snooping was introduced to prevent rogue DHCP servers and build a trusted binding table.

### Design Principles

- Enable only on relevant VLANs
- Trust uplinks and server-facing ports
- Keep client access ports untrusted
- Disable Option 82 (critical for this lab)

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
- Can't find dhcp server on VPCS
- Static IP connectivity worked fine

---

## Root Cause

The issue was caused by:
- Incorrect trust boundaries
- DHCP Snooping blocking legitimate responses
- Option 82 insertion interfering with DHCP relay

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

DAI was implemented after DHCP Snooping to prevent ARP spoofing.

### Notes
- Enabled only on VLANs present on each switch
- Uplinks must be trusted
- Static devices require special handling

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

This lab highlights a real-world lesson:

Security features like DHCP Snooping and DAI must be carefully integrated, as misconfiguration can break core services like DHCP.

Understanding traffic flow is critical when implementing Layer 2 security.

---
