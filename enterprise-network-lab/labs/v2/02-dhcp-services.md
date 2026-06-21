# 02 - DHCP Services

## Overview

This phase adds centralised DHCP services to the V2 Enterprise Network Lab.

In V1, endpoint addressing was configured manually using static IP addresses. In V2, DHCP is introduced to make the lab behave more like a real enterprise network, where client devices receive IP addressing, default gateway, and DNS information automatically.

A Windows Server VM named **DC1** is deployed in the HQ server VLAN and provides DHCP services for client networks across HQ and the branch sites.

This phase introduces:

- Centralised DHCP services from DC1
- DHCP scopes for client VLANs
- DHCP relay using `ip helper-address`
- DHCP option configuration
- DHCP Snooping integration
- Dynamic ARP Inspection considerations
- Troubleshooting of DHCP relay and Layer 2 security interactions

---

## Objective

The objective of this phase is to provide dynamic IP addressing to internal client networks while preserving the Layer 2 security controls introduced in V1.

This includes:

- Installing and configuring DHCP services on DC1
- Creating DHCP scopes for client VLANs
- Configuring DHCP options for default gateway and DNS
- Configuring DHCP relay on routed interfaces
- Validating DHCP lease assignment
- Ensuring DHCP works across VLAN and WAN boundaries
- Adjusting DHCP Snooping trust boundaries
- Ensuring DAI does not block legitimate client traffic
- Documenting troubleshooting steps and lessons learned

---

## Topology Reference

The screenshot below shows the V2 EVE-NG topology.

![V2 EVE-NG Topology](../../topology/v2/v2-eve-ng-topology.png)

DC1 is located in the HQ server VLAN and provides DHCP services to clients in multiple networks.

---

## Relationship to V1

V1 used static addressing for endpoint devices.

V2 introduces centralised DHCP while keeping the existing V1 network foundation.

| Area | V1 | V2 |
|---|---|---|
| Endpoint addressing | Static IP addresses | DHCP introduced for client VLANs |
| DHCP server | Not included | DC1 provides DHCP |
| DHCP relay | Not required | Added on Layer 3 gateways |
| DHCP Snooping | Configured as Layer 2 security | Adjusted to support real DHCP traffic |
| DAI | Static bindings / trusted interfaces | Reviewed due to DHCP Snooping dependency |
| Server VLAN | Static server addressing | DC1 remains statically addressed |

---

## DHCP Server Design

The DHCP server is hosted on **DC1**.

| Setting | Value |
|---|---|
| Hostname | DC1 |
| Server Role | DHCP, DNS, Active Directory Domain Services |
| VLAN | VLAN 20 - Servers |
| IP Address | 192.168.20.10 |
| Subnet Mask | 255.255.255.0 |
| Default Gateway | 192.168.20.1 |
| DNS Server | 192.168.20.10 |

DC1 uses a static IP address because infrastructure servers should keep predictable addressing.

---

## DHCP Server Verification

The screenshot below shows DC1 configured with the correct server VLAN addressing.

<img width="702" height="347" alt="DC1 IP configuration" src="https://github.com/user-attachments/assets/36d6f666-5b47-48e8-9d3a-a43734ab8258" />

---

## DHCP Scope Design

DHCP scopes were created for client networks.

| Scope | Network | Purpose | Default Gateway | DNS Server |
|---|---|---|---|---|
| Admin Scope | 192.168.10.0/24 | Admin clients | 192.168.10.1 | 192.168.20.10 |
| Engineering Scope | 192.168.30.0/24 | Engineering clients | 192.168.30.1 | 192.168.20.10 |
| Sales Scope | 192.168.40.0/24 | Sales clients | 192.168.40.1 | 192.168.20.10 |

The server VLAN remains statically addressed for infrastructure devices such as DC1 and the application server.

---

## DHCP Scope Verification

The screenshots below show DHCP scopes configured on DC1.

<img width="1089" height="812" alt="DHCP scope configuration" src="https://github.com/user-attachments/assets/353c7e1b-ad51-4009-b4b1-3b7eb43e0cde" />

---

<img width="1085" height="812" alt="DHCP scope options" src="https://github.com/user-attachments/assets/01a9316d-6810-4cfa-a26e-a1fb370acf3e" />

---

## DHCP Options

Each DHCP scope provides the required client network settings.

Important DHCP options include:

| Option | Purpose |
|---|---|
| Default Gateway | Tells clients which router/SVI to use for off-subnet traffic |
| DNS Server | Points clients to DC1 for DNS resolution |
| Domain Name | Allows clients to use the internal `lab.local` domain |

Example DNS setting:

```text
192.168.20.10
```

Example domain name:

```text
lab.local
```

This prepares clients for later DNS and Active Directory integration.

---

## DHCP Relay Design

DHCP uses broadcast messages during the initial address discovery process.

Because DC1 is located in VLAN 20, clients in other VLANs and branch networks cannot reach the DHCP server using broadcasts directly.

DHCP relay is required on Layer 3 gateways.

The relay agent forwards DHCP requests to DC1 using:

```cisco
ip helper-address 192.168.20.10
```

---

## DHCP Relay Placement

DHCP relay is configured on the Layer 3 interfaces that act as client default gateways.

| Network | Gateway Device | Interface Type | DHCP Relay Required |
|---|---|---|---|
| Admin VLAN 10 | HQ-CSW1 / HQ-CSW2 | SVI | Yes |
| Engineering VLAN 30 | BR1 | Router LAN interface | Yes |
| Sales VLAN 40 | BR2 | Router LAN interface | Yes |
| Server VLAN 20 | HQ-CSW1 / HQ-CSW2 | SVI | Not required for DC1 itself |

The server VLAN contains the DHCP server, so relay is not required for DC1.

---

## Example DHCP Relay Configuration

### Admin VLAN Relay

The Admin VLAN uses HSRP on the HQ core switches.

The helper address is configured on the VLAN 10 SVI so DHCP requests can be forwarded to DC1.

```cisco
interface Vlan10
 ip helper-address 192.168.20.10
```

In an HSRP design, the helper address should be configured consistently on both core switches so DHCP relay remains available during gateway failover.

---

### Engineering Branch Relay

BR1 acts as the default gateway for the Engineering branch.

```cisco
interface GigabitEthernet0/0
 description ENGINEERING LAN
 ip helper-address 192.168.20.10
```

---

### Sales Branch Relay

BR2 acts as the default gateway for the Sales branch.

```cisco
interface GigabitEthernet0/0
 description SALES LAN
 ip helper-address 192.168.20.10
```

---

## Expected DHCP Behaviour

After DHCP is configured correctly:

- Clients send DHCP Discover messages
- The local Layer 3 gateway relays the request to DC1
- DC1 assigns an address from the correct scope
- Clients receive the correct subnet mask
- Clients receive the correct default gateway
- Clients receive DC1 as their DNS server
- Clients can communicate across the network using the existing OSPF routing design

Example client assignments:

| Client Network | Example DHCP Address |
|---|---|
| Admin | 192.168.10.50 |
| Engineering | 192.168.30.50 |
| Sales | 192.168.40.50 |

---

## DHCP Snooping Integration

DHCP Snooping was already introduced in V1 as a Layer 2 security feature.

When DHCP was added in V2, DHCP Snooping had to be reviewed because legitimate DHCP traffic now needed to pass through the switching infrastructure.

DHCP Snooping protects the network by:

- Blocking rogue DHCP servers on untrusted ports
- Allowing DHCP server responses only from trusted paths
- Building a DHCP Snooping binding table
- Supporting Dynamic ARP Inspection

However, if trust boundaries are configured incorrectly, legitimate DHCP traffic can be dropped.

---

## DHCP Snooping Design Principles

The DHCP Snooping design follows these rules:

- Client-facing access ports remain untrusted
- Infrastructure uplinks are trusted
- Ports facing the DHCP server are trusted
- DHCP Snooping is enabled only on relevant VLANs
- Option 82 is disabled where required for DHCP relay compatibility

---

## DHCP Snooping Option 82

During testing, DHCP relay and DHCP Snooping did not initially work together as expected.

One issue was DHCP Snooping Option 82 insertion.

Option 82 adds relay information into DHCP packets. In this lab, the Windows DHCP server configuration did not accept the modified relay information correctly, which contributed to DHCP failure.

The issue was resolved by disabling Option 82 insertion on the switches:

```cisco
no ip dhcp snooping information option
```

This allowed relayed DHCP requests to complete successfully.

---

## Example DHCP Snooping Configuration - Access Switches

On HQ access switches, uplinks towards the core are trusted.

Client access ports remain untrusted.

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

## Example DHCP Snooping Configuration - Server Switches

On server switches, the uplinks are trusted and the DHCP server-facing path must allow DHCP server responses.

```cisco
ip dhcp snooping
ip dhcp snooping vlan 10,20
no ip dhcp snooping information option

interface GigabitEthernet0/0
 ip dhcp snooping trust

interface GigabitEthernet0/1
 ip dhcp snooping trust
```

If the DHCP server is directly connected to a server switch access port, that port must also be trusted for DHCP Snooping:

```cisco
interface GigabitEthernet0/2
 ip dhcp snooping trust
```

This allows legitimate DHCP Offer and ACK messages from DC1 to pass through the switch.

---

## Example DHCP Snooping Configuration - Branch Switches

On branch switches, the router-facing uplink is trusted because DHCP replies return through the branch router.

### Engineering Branch

```cisco
ip dhcp snooping
ip dhcp snooping vlan 30
no ip dhcp snooping information option

interface GigabitEthernet0/0
 ip dhcp snooping trust
```

### Sales Branch

```cisco
ip dhcp snooping
ip dhcp snooping vlan 40
no ip dhcp snooping information option

interface GigabitEthernet0/0
 ip dhcp snooping trust
```

Client-facing ports remain untrusted.

---

## Dynamic ARP Inspection Considerations

Dynamic ARP Inspection depends on the DHCP Snooping binding table.

When clients receive addresses through DHCP, the switch learns valid IP-to-MAC-to-port mappings in the DHCP Snooping database.

DAI can then use that information to validate ARP traffic.

This means DHCP Snooping must work correctly before DAI can reliably protect dynamically addressed clients.

---

## Static IP Device Consideration

Some devices in the lab remain statically addressed, such as servers and network infrastructure.

Static IP devices do not automatically appear in the DHCP Snooping binding table.

This can cause DAI to drop legitimate ARP traffic unless the device is handled separately.

Possible approaches include:

- Static source bindings
- ARP ACLs
- Trusting specific infrastructure-facing interfaces where appropriate

In this lab, a simplified approach was used where required to keep connectivity working in the virtual environment.

In production, ARP ACLs or more precise static bindings would normally be preferred over trusting endpoint-facing ports.

---

## Example DAI Configuration

Example DAI configuration:

```cisco
ip arp inspection vlan 30

interface GigabitEthernet0/0
 ip arp inspection trust
```

The trusted interface should match the expected infrastructure path.

Client access ports should remain untrusted where possible.

---

## Troubleshooting Process

After DHCP was configured, clients initially failed to obtain addresses.

The following symptom was observed on VPCS clients:

<img width="882" height="215" alt="DHCP failure on VPCS client" src="https://github.com/user-attachments/assets/9adb367d-aad4-4185-ada6-82095bc0104e" />

The issue was investigated step by step.

---

## Initial Observations

The following observations helped narrow down the issue:

- Static IP addressing worked correctly
- Inter-VLAN routing was functional
- OSPF routing was operational
- DC1 was reachable from client VLANs
- DHCP scopes were configured and active
- Only DHCP lease assignment was failing

This suggested that the issue was not basic routing.

The problem was likely related to DHCP relay, DHCP Snooping, or Layer 2 security behaviour.

---

## Root Cause

The DHCP issue was caused by the interaction between DHCP relay and DHCP Snooping.

The main issues were:

- DHCP Snooping trust boundaries were not aligned with the actual DHCP traffic path
- DHCP server responses were being dropped on untrusted interfaces
- Option 82 insertion caused compatibility issues with the DHCP server configuration
- DAI also required consideration because it depends on DHCP Snooping bindings

This prevented clients from completing the DHCP DORA process.

---

## Resolution

The issue was resolved by correcting the DHCP Snooping design.

The resolution included:

- Trusting uplinks and DHCP server-facing paths
- Keeping client-facing ports untrusted
- Disabling DHCP Snooping Option 82 insertion
- Ensuring DHCP Snooping was enabled only on required VLANs
- Reviewing DAI trust boundaries
- Handling static IP devices separately where required

The key command used to resolve the Option 82 issue was:

```cisco
no ip dhcp snooping information option
```

After these changes, clients were able to receive DHCP leases successfully.

---

## DHCP Validation

The following validation tests were performed:

- DHCP address assignment from Admin VLAN
- DHCP address assignment from Engineering VLAN
- DHCP address assignment from Sales VLAN
- Ping from DHCP clients to their default gateway
- Ping from DHCP clients to DC1
- DNS server assignment checked on clients
- DHCP Snooping binding table checked
- DAI checked to ensure legitimate ARP traffic was not blocked

---

## Validation Evidence

The screenshot below shows DHCP-related validation after correcting the issue.

<img width="886" height="411" alt="DHCP validation output" src="https://github.com/user-attachments/assets/5f072355-4bd6-423c-9a63-e96903565038" />

---

## Verification Commands

The following commands were useful during DHCP validation and troubleshooting.

| Command | Purpose |
|---|---|
| `show ip dhcp snooping` | Confirms DHCP Snooping status |
| `show ip dhcp snooping binding` | Shows DHCP Snooping learned bindings |
| `show ip arp inspection vlan 10` | Checks DAI operation for VLAN 10 |
| `show ip arp inspection vlan 30` | Checks DAI operation for VLAN 30 |
| `show ip arp inspection vlan 40` | Checks DAI operation for VLAN 40 |
| `show interfaces status` | Confirms port status and VLAN assignment |
| `show interfaces trunk` | Confirms trunk links and allowed VLANs |
| `show running-config | section dhcp` | Reviews DHCP Snooping configuration |
| `show running-config interface <interface>` | Checks interface trust configuration |
| `ipconfig /all` | Confirms DHCP settings on Windows clients |
| `ping 192.168.20.10` | Tests reachability to DC1 |

---

## Expected Results

At the end of this phase:

- DC1 provides DHCP services
- DHCP scopes exist for client VLANs
- Clients receive dynamic IP addresses
- Clients receive the correct default gateway
- Clients receive DC1 as their DNS server
- DHCP relay forwards requests across Layer 3 boundaries
- DHCP Snooping allows legitimate DHCP traffic
- Client access ports remain untrusted
- Uplinks and DHCP server-facing paths are trusted
- Option 82 insertion is disabled where required
- DAI does not block legitimate client ARP traffic
- Static IP devices are handled separately
- Existing V1 routing and segmentation remain operational

---

## Design Notes

### Why DHCP Was Centralised

Centralising DHCP on DC1 reflects a common enterprise design.

It allows addressing to be managed from one server instead of configuring local DHCP services at every VLAN or branch.

### Why DHCP Relay Was Required

DHCP Discover messages are broadcasts and do not cross Layer 3 boundaries by default.

Because DC1 is in VLAN 20 and clients exist in other VLANs, relay agents are required on client gateway interfaces.

### Why DHCP Snooping Needed Adjustment

DHCP Snooping was already protecting the access layer.

Once real DHCP services were introduced, the trust boundaries had to match the actual DHCP traffic path.

Otherwise, the security feature blocked legitimate DHCP traffic.

### Why Option 82 Was Disabled

Option 82 insertion caused compatibility issues in this lab.

Disabling it allowed relayed DHCP requests and responses to complete correctly.

### Why DAI Needed Reviewing

DAI relies on the DHCP Snooping binding table.

If DHCP Snooping is not working, DAI can incorrectly block ARP traffic.

Static devices also require special handling because they do not automatically appear in DHCP Snooping bindings.

---

## Platform Note

This lab was built using virtual Cisco images in EVE-NG.

Some default or platform-generated CLI output may vary between devices. The published configurations and documentation focus on the relevant working configuration and design intent.

---

## Outcome

Centralised DHCP was successfully implemented in V2.

This phase added:

- DHCP services on DC1
- DHCP scopes for client VLANs
- DHCP relay across Layer 3 boundaries
- DHCP Snooping trust boundary refinement
- Option 82 troubleshooting
- DAI validation
- Dynamic client addressing across the enterprise lab

At this stage, the V2 network supports dynamic addressing for client networks while retaining the Layer 2 security controls introduced in V1.

---

## Key Learning

This phase reinforced several important enterprise networking concepts:

- DHCP relay is required when clients and DHCP servers are in different subnets
- DHCP Snooping must be carefully aligned with real traffic flow
- Trusted and untrusted ports must be selected correctly
- Option 82 can affect DHCP relay behaviour
- DAI depends on valid DHCP Snooping bindings
- Static IP devices require additional planning when DAI is enabled
- Security features can break core services if they are not integrated carefully
- Troubleshooting should confirm routing first before focusing on Layer 2 control-plane features

---

## Related Documentation

- [Previous: 01 - Internet Edge and NAT](01-internet-%26-edge.md)
- [Next: 03 - DNS and Domain Services](03-dns-%26-domain-services.md)
- [V2 Overview](./)
- [V2 Topology](../../topology/v2/)
- [V2 Configuration Changes](../../configs/changes-v2/)
- [V1 Access Layer](../v1/02-access-layer.md)

---
