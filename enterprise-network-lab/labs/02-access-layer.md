# 02 - Access Layer Configuration

## Objective

Configure the access layer switches to provide connectivity for end devices, enforce VLAN segmentation, and ensure secure and efficient Layer 2 operation.

This phase includes:

- VLAN implementation on access switches
- Trunk configuration to the core layer
- Access port configuration for end devices
- Layer 2 security features
- End-device IP configuration and connectivity testing

---

## Access Layer Design

The access layer consists of:

- HQ-ASW1 / HQ-ASW2 (Admin users - VLAN 10)
- HQ-SSW1 / HQ-SSW2 (Servers - VLAN 20)

Each access switch is dual-homed to both core switches for redundancy.

The access layer operates at Layer 2, with all routing handled by the core switches.

---

## VLAN Configuration

The required VLANs were created on all access switches to match the core configuration.

```bash
vlan 10
 name ADMIN

vlan 20
 name SERVERS
```

---

# 📄 Part 2 (Trunks + uplinks)

---

## Uplink Configuration (Trunk Links)

Uplink interfaces connecting access switches to the core were configured as trunk ports.

```bash
interface range gi0/0 - 1
 switchport trunk encapsulation dot1q
 switchport mode trunk
 switchport trunk allowed vlan 10,20
 switchport nonegotiate
```

### Key Points
- 802.1Q encapsulation was required due to the switch platform
- VLANs 10 and 20 were allowed across all trunk links
- DTP was disabled to prevent automatic trunk negotiation
- Dual uplinks provide redundancy to both core switches

---

# 📄 Part 3 (Access ports + security)

---

## Access Port Configuration

Access ports were configured to assign end devices to the correct VLAN.

### Admin Switches (VLAN 10)

```bash
interface gi0/2
 switchport mode access
 switchport access vlan 10
 spanning-tree portfast
 spanning-tree bpduguard enable
```

### Server Switches (VLAN 20)

```bash
interface gi0/2
 switchport mode access
 switchport access vlan 20
 spanning-tree portfast
 spanning-tree bpduguard enable
```

---

## Layer 2 Security Features

Several security features were implemented on access ports:

### PortFast
- Allows immediate transition to forwarding state
- Reduces device startup delay
### BPDU Guard
- Protects against rogue switches
- Automatically disables port if BPDU is received

These features help maintain network stability and security at the access layer.

---

# 📄 Part 4 (IP config + testing)

---

## End Device Configuration

End devices were configured with static IP addressing.

### Admin PCs

```text
IP Address: 192.168.10.10
Subnet Mask: 255.255.255.0
Default Gateway: 192.168.10.1
```

### Servers

```text
IP Address: 192.168.20.10
Subnet Mask: 255.255.255.0
Default Gateway: 192.168.20.1
```
---

## Connectivity Testing

The following tests were performed to validate the network:

### Layer 2 Testing
PC to PC communication within the same VLAN

### Gateway Reachability

```bash
ping 192.168.10.1
ping 192.168.20.1
```
---

### Inter-VLAN Routing

```bash
ping 192.168.20.10
```

---

## Observations
- Devices within the same VLAN successfully communicated via Layer 2 switching
- Default gateway reachability confirmed correct SVI operation on the core
- Inter-VLAN communication verified that routing is functioning correctly
- Initial packet loss was observed due to ARP resolution, which is expected behaviour

---


---

# 📄 Part 5 (Troubleshooting)

---

## Issue Encountered: SVI Down

During testing, the default gateway was initially unreachable.

This was caused by the VLAN interface (SVI) being in a down/down state on the core switch.

The issue was resolved by enabling the interface:

```bash
interface vlan 10
 no shutdown
```

This demonstrates that an SVI requires:

- The VLAN to exist
- Active Layer 2 participation
- The interface to be administratively up

---

# 📄 Part 6 (Implementing DAI)

---

## Dynamic ARP Inspection (DAI)

Dynamic ARP Inspection (DAI) was implemented on the access layer switches to protect against ARP spoofing and man-in-the-middle attacks.

DAI works by validating ARP packets against a trusted database of IP-to-MAC bindings before allowing them to be forwarded.

---

### Configuration

DAI was enabled for the relevant VLANs:

```bash
ip arp inspection vlan 10,20
```

---

## DHCP Snooping Dependency

DAI relies on DHCP Snooping to build a binding table of valid IP-to-MAC mappings.

```bash
ip dhcp snooping
ip dhcp snooping vlan 10,20
```

---

## Trusted Interfaces (Uplinks)

Uplink interfaces toward the core switches were configured as trusted:

```bash
interface range gi0/0 - 1
 ip dhcp snooping trust
 ip arp inspection trust
```

---

## Untrusted Interfaces (Access Ports)

All access ports remain untrusted by default.
- ARP packets received on these ports are inspected
- Invalid ARP packets are dropped.

---

## Static IP Addressing Consideration

Since this lab uses static IP addressing instead of DHCP, manual bindings were required to allow DAI to function correctly.

### Example:
```bash
ip source binding 5000.0002.0000 vlan 20 192.168.20.10 interface gi0/2
```
Without these bindings, ARP traffic is dropped and devices cannot communicate.

---

## Verification

DAI operation was verified using:

```bash
show ip arp inspection statistics
show ip arp inspection interfaces
show ip source binding
```

---

### Observations
- ARP traffic from valid devices was successfully permitted
- ARP traffic was dropped when bindings were missing
- Connectivity was restored after adding correct bindings

This confirms that DAI is actively inspecting and protecting Layer 2 traffic.

---

### Design Justification

DAI was applied at the access layer only, as this is where end devices connect and where ARP-based attacks originate.

Core switches were not configured with DAI, as they operate as trusted infrastructure devices.

---

## Summary

The access layer successfully provides:

- VLAN-based segmentation
- Secure access port configuration
- Redundant uplinks to the core
- Reliable connectivity for end devices

At this stage, full LAN connectivity is operational across VLANs, with routing handled by the core layer.
