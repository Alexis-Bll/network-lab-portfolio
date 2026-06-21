# Lab Documentation

This folder contains the versioned documentation for the **Enterprise Network Lab**.

The lab is split into two defined stages:

- **V1** - Completed enterprise network foundation
- **V2** - Enterprise services expansion, currently in progress

This structure shows how the lab developed from a core CCNA-level enterprise network into a more realistic environment with additional infrastructure services.

---

## Documentation Structure

| Version | Status | Description |
|---|---|---|
| [V1](v1/) | Completed | Core enterprise network lab covering routing, switching, redundancy, segmentation, ACLs, and Layer 2 security |
| [V2](v2/) | In Progress | Enterprise services expansion introducing internet access, NAT, DHCP, DNS, Active Directory, Group Policy, and application server integration |

---

## V1 - Core Enterprise Network Lab

V1 is the completed foundation of the lab.

It demonstrates a multi-site enterprise network with HQ and branch connectivity, using technologies commonly covered in the Cisco CCNA.

### V1 Includes

- Base device configuration
- VLANs and trunking
- Inter-VLAN routing using SVIs
- HQ core and access layer switching
- OSPF routing across the enterprise WAN
- HSRP gateway redundancy
- LACP EtherChannel
- Branch switch configuration
- Access Control Lists for traffic filtering
- Layer 2 security controls
- End-to-end testing and validation

### V1 Documentation

- [V1 Overview](v1/)
- [00 - Base Configuration](v1/00-base-config.md)
- [01 - Core Setup](v1/01-core-setup.md)
- [02 - Access Layer](v1/02-access-layer.md)
- [03 - Routing with OSPF](v1/03-routing-ospf.md)
- [04 - Branch Switches](v1/04-branch-switches.md)
- [05 - Access Control Lists](v1/05-access-control-lists.md)
- [06 - Testing and Validation](v1/06-testing.md)

---

## V2 - Enterprise Services Expansion

V2 builds on the completed V1 network by adding additional services found in real enterprise environments.

The purpose of V2 is to show how a working network can be expanded without redesigning the entire topology.

### V2 Includes

- Internet edge connectivity
- NAT/PAT for outbound internet access
- Default route advertisement into OSPF
- Centralised DHCP services
- DHCP relay using `ip helper-address`
- DNS services
- Active Directory Domain Services
- Domain-joined clients
- Organisational Units, users, and groups
- Group Policy testing
- Application server integration

### V2 Documentation

- [V2 Overview](v2/)
- [01 - Internet and Edge Connectivity](v2/01-internet-%26-edge.md)
- [02 - DHCP Services](v2/02-dhcp-services.md)
- [03 - DNS and Domain Services](v2/03-dns-%26-domain-services.md)
- [04 - Application Server Integration](v2/04-application-server-integration.md)
- [05 - Testing and Validation](labs/v2/05-testing-validation.md)


> Note: V2 is currently in progress and will be completed after the V1 documentation has been fully reviewed and polished.

---

## Purpose of the Lab Documentation

The documentation is written to show more than just final configuration commands.

It is intended to demonstrate:

- The reason behind each design choice
- The order in which the network was built
- How each technology fits into the overall topology
- How connectivity and redundancy were validated
- How issues were identified and resolved during implementation
- How the lab evolved from a network-only design into a more complete enterprise environment

---

## Related Sections

- [Enterprise Network Lab Overview](../)
- [Device Configurations](../configs/)
- [Topology Documentation](../topology/)

---
