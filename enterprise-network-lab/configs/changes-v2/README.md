# V2 Configuration Changes

## Overview

This folder contains configuration snippets and notes highlighting the key changes introduced in V2 of the Enterprise Network Lab.

Rather than duplicating full device configurations, this section focuses only on the additional components required to extend the original V1 design.

---

## Key Changes

### Internet Edge & NAT
- NAT (PAT) configured on HQ-R1
- Internal private IP addresses translated for outbound internet access
- Introduction of a clear inside/outside boundary

---

### Centralised Services
- Deployment of DC1 providing:
  - DHCP (dynamic IP allocation)
  - DNS (name resolution)
  - Active Directory (domain services)

---

### Application Server
- Addition of an application server within the server VLAN
- Simulates real-world service hosting within an enterprise network

---

### Routing Updates
- Minor updates to support external connectivity
- Existing OSPF design retained with no major structural changes

---

## Design Approach

The V2 implementation builds incrementally on the V1 design:

- Core architecture remains unchanged  
- Enhancements are layered on top of the existing network  
- Changes reflect how real enterprise environments evolve over time  

---

## Notes

This folder is intended to highlight differences between versions rather than provide full configurations.

For complete device configurations, refer to the main `configs/` directory.
