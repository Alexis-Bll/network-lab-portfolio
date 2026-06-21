# Enterprise Network Lab - V2

## Overview

V2 is the enterprise services expansion of the **Enterprise Network Lab**.

It builds on the completed V1 network foundation by introducing additional services commonly found in real enterprise environments, including internet access, NAT, DHCP, DNS, Active Directory, Group Policy, and application server integration.

V1 focused on the core network.  
V2 focuses on expanding that network with infrastructure services.

---

## V2 Status

**Status:** In Progress

V2 is currently being reviewed, completed, and polished after the V1 documentation was fully finalised.

Some V2 phases are already documented, while others are still being completed.

---

## Purpose of V2

The purpose of V2 is to show how a working enterprise network can be expanded without redesigning the entire topology.

This reflects a realistic environment where:

- The core network already exists
- New services are introduced over time
- Internet access is added through the existing HQ edge
- Centralised services are deployed from the server VLAN
- Client devices are updated to use DHCP, DNS, and domain services
- Security and segmentation are maintained while functionality is expanded

V2 is the final planned expansion of this lab.

---

## Relationship to V1

V2 does not replace the V1 design.

Instead, it builds on the completed V1 baseline.

| Area | V1 | V2 |
|---|---|---|
| Network foundation | Completed | Reused |
| VLANs and routing | Completed | Reused |
| OSPF | Completed | Extended with default route advertisement |
| HSRP | Completed | Retained |
| ACLs | Completed | Retained and expanded where required |
| Layer 2 security | Completed | Adjusted for DHCP and DAI behaviour |
| Internet access | Not included | Added |
| NAT/PAT | Not included | Added |
| DHCP | Static addressing | Centralised DHCP introduced |
| DNS | Not included | Added through Windows Server |
| Active Directory | Not included | Added |
| Group Policy | Not included | Added |
| Application services | Not included | Added as final service layer |

---

## V2 Scope

### Included in V2

V2 introduces:

- Internet edge connectivity
- NAT/PAT on the HQ edge router
- Default route advertisement into OSPF
- Centralised DHCP services from Windows Server
- DHCP relay using `ip helper-address`
- DNS services
- Active Directory Domain Services
- Domain-joined clients
- Organisational Units, users, and groups
- Group Policy testing
- Application server integration
- Final V2 testing and validation

### Not Included in V2

V2 is not intended to become an endless expansion of the lab.

The following areas are outside the scope of this version:

- Additional lab versions after V2
- Firewall replacement or Fortinet integration
- Wireless networking
- VPN services
- Cloud integration
- Monitoring platforms
- Full production hardening

Those topics may be explored separately in the future, but they are not part of this Enterprise Network Lab progression.

---

## V2 Documentation Structure

| Phase | Document | Status | Focus |
|---|---|---|---|
| 01 | [Internet Edge and NAT](01-internet-%26-edge.md) | Needs Review | Internet access, NAT/PAT, default route, OSPF default advertisement |
| 02 | [DHCP Services](02-dhcp-services.md) | Needs Review | Centralised DHCP, DHCP relay, DHCP Snooping, DAI considerations |
| 03 | [DNS and Domain Services](03-dns-%26-domain-services.md) | Needs Review | DNS, Active Directory, domain joins, users, groups, permissions, GPO |
| 04 | [Application Server Integration](04-application-server-integration.md) | In Progress | Application server placement, access testing, service validation |
| 05 | [Testing and Validation](05-testing-validation.md) | In Progress | Final V2 validation, NAT, DHCP, DNS, AD, GPO, and application testing |

---

## Recommended Reading Order

Review the V2 documentation in this order:

1. [Internet Edge and NAT](01-internet-%26-edge.md)
2. [DHCP Services](02-dhcp-services.md)
3. [DNS and Domain Services](03-dns-%26-domain-services.md)
4. [Application Server Integration](04-application-server-integration.md)
5. [Testing and Validation](05-testing-validation.md)

This follows the order in which enterprise services are added to the existing V1 network.

---

## V2 Design Summary

V2 expands the original private enterprise WAN by adding an internet edge and centralised server services.

The main design changes are:

- HQ-R1 becomes the internet/NAT edge router
- A simulated external network is added beyond the HQ edge
- Internal networks use NAT/PAT for outbound connectivity
- A Windows Server is introduced as DC1
- DC1 provides DHCP, DNS, and Active Directory services
- Client devices receive network settings through DHCP
- DNS resolution is handled through the domain controller
- Clients are joined to the `lab.local` domain
- Users and groups are organised for access control testing
- Group Policy is used to demonstrate centralised endpoint control
- An application server is added to test internal service access

---

## Key Technologies Introduced in V2

V2 introduces or expands the following technologies:

- NAT/PAT
- Default static route
- OSPF default route advertisement
- DHCP relay
- Centralised DHCP scopes
- DHCP Snooping trust boundaries
- Dynamic ARP Inspection considerations
- DNS
- Active Directory Domain Services
- Domain-joined clients
- Organisational Units
- Security groups
- Shared folder permissions
- Group Policy Objects
- Application server access testing

---

## Internet Edge and NAT

V2 adds simulated internet access from the HQ edge.

This includes:

- Configuring an outside interface on HQ-R1
- Configuring NAT inside and outside interfaces
- Creating a NAT access list for internal networks
- Configuring PAT overload
- Adding a default route towards the simulated internet
- Advertising the default route into OSPF

This allows internal VLANs and branch networks to reach external destinations through the HQ edge.

---

## DHCP Services

V2 introduces centralised DHCP from a Windows Server.

This includes:

- DHCP scopes for internal VLANs
- Default gateway assignment
- DNS server assignment
- DHCP relay using `ip helper-address`
- DHCP lease validation from client devices
- Troubleshooting DHCP Snooping and DHCP relay behaviour

This moves the lab from static addressing towards a more realistic enterprise client addressing model.

---

## DNS and Domain Services

V2 introduces DNS and Active Directory Domain Services.

This includes:

- Creating the `lab.local` domain
- Configuring DNS on DC1
- Joining clients across multiple VLANs to the domain
- Creating OUs, users, and groups
- Testing authentication
- Testing shared folder permissions
- Applying Group Policy restrictions

This demonstrates how network connectivity supports real infrastructure services.

---

## Application Server Integration

V2 includes an internal application server as the final service layer.

This phase is used to demonstrate:

- Server placement within the network
- Internal application reachability
- Client access from different VLANs
- Access control considerations
- End-to-end service validation

This section is still in progress and will be completed before V2 is signed off.

---

## Testing and Validation

The final V2 testing phase will validate the completed services expansion.

Testing should confirm:

- Internal clients can reach the simulated internet through NAT
- DHCP clients receive correct addressing
- DNS resolution works correctly
- Domain-joined clients authenticate successfully
- Group Policy applies as expected
- Application services are reachable from permitted networks
- Existing V1 behaviour remains stable
- HSRP and OSPF continue to operate as expected

This ensures V2 is not just configured, but properly validated.

---

## Documentation Approach

Each V2 phase should explain:

- The purpose of the change
- What was added to the existing V1 network
- Why the change was required
- The key configuration steps
- Verification commands or screenshots
- Issues encountered during testing
- Troubleshooting notes
- Final outcome

The goal is to show both technical implementation and practical troubleshooting ability.

---

## Related Documentation

- [V1 Lab Documentation](../v1/)
- [Enterprise Network Lab Overview](../../)
- [Device Configurations](../../configs/)
- [V2 Configuration Changes](../../configs/changes-v2/)
- [V2 Topology](../../topology/v2/)

---

## Summary

V2 extends the completed V1 network foundation by adding enterprise services and internet connectivity.

It demonstrates how routing, switching, redundancy, segmentation, NAT, DHCP, DNS, Active Directory, Group Policy, and application services can work together in a more realistic business network.

Once completed, V2 will represent the final planned state of the Enterprise Network Lab.
