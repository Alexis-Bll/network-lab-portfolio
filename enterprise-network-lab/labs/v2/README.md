# Lab Documentation – V2

This folder contains structured documentation for the **V2 implementation** of the enterprise network lab.

V2 builds upon the completed V1 design by introducing additional real-world enterprise features, including internet connectivity, NAT, and centralised services.

The lab continues to follow a phased approach to reflect how changes and enhancements are implemented in production environments.

---

## Approach

Unlike V1, which represents a full end-to-end deployment, V2 focuses on **incremental enhancements** to an existing network.

This reflects real-world enterprise scenarios where:

- Core infrastructure is already in place  
- New services are introduced over time  
- Changes are implemented with minimal disruption  

Each phase highlights the specific configurations and reasoning required to extend the V1 design.

---

## Structure

The V2 lab is divided into targeted phases, focusing only on the additional components introduced:

### 01 - Internet Edge & NAT
Implementation of external connectivity including:

- ISP-facing interface configuration  
- NAT (PAT) setup on HQ-R1  
- Inside/outside interface designation  
- Access control for NAT traffic  
- Verification of outbound internet access  

---

### 02 - DHCP Services
Deployment of centralised DHCP services including:

- DHCP scope configuration on DC1  
- VLAN-based IP address allocation  
- Default gateway and DNS assignment  
- Transition from static to dynamic addressing  
- Client lease verification  

---

### 03 - DNS & Domain Services
Integration of core network services including:

- DNS configuration on DC1  
- Name resolution testing  
- (Optional) Active Directory domain setup  
- Client domain connectivity  

---

### 04 - Application Server Integration
Deployment of internal services including:

- Application server placement within the server VLAN  
- Network connectivity validation  
- Service accessibility from multiple VLANs  
- Basic access control considerations  

---

### 05 - Testing & Validation (V2)
Validation of the enhanced network including:

- Internal → Internet connectivity (via NAT)  
- DHCP address assignment across VLANs  
- DNS resolution testing  
- Failover validation (HSRP + OSPF unchanged behaviour)  
- End-to-end connectivity across all sites  

---

## Documentation Approach

Each phase includes:

- Objective of the enhancement  
- Configuration steps  
- Key commands  
- Verification procedures  
- Observations and expected behaviour  

The focus is on demonstrating both **how the configuration is implemented** and **why the changes are required**.

---

## Relationship to V1

- V1 provides the **baseline enterprise network design**  
- V2 extends this design without replacing it  
- Core routing, switching, and security configurations remain unchanged  

This approach reflects how enterprise networks evolve through controlled, incremental updates rather than full redesigns.

---

## Notes

This lab is designed to demonstrate progression beyond core CCNA concepts into more practical, real-world networking scenarios.

The implementation focuses on:

- Integration with external networks  
- Service centralisation  
- Maintaining security and segmentation while expanding functionality  

Further enhancements may be added as part of ongoing development.
