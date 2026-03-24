# Lab Documentation

This folder contains structured documentation for each phase of the enterprise network lab.

Each lab file explains the configuration steps, design decisions, and verification processes used to build the network from the ground up.

---

## Structure

The lab is divided into multiple phases to reflect a real-world network deployment approach:

* **00 - Base Configuration**

  * Initial device setup including hostnames, SSH, local authentication, and secure access

* **01 - Core Layer Setup**

  * VLAN creation, trunking, EtherChannel, SVIs, HSRP, and STP tuning

* **02 - Access Layer**

  * Access switch configuration, VLAN assignment, and end-device connectivity

* **03 - Routing & WAN**

  * OSPF configuration and inter-site connectivity via the simulated service provider network

* **04 - Branch Integration**

  * Branch site configuration and connectivity to HQ

* **05 - Testing & Failover**

  * Validation of redundancy, failover scenarios, and network resilience

---

## Documentation Approach

Each lab file includes:

* Objective of the phase
* Configuration steps
* Relevant commands
* Verification procedures
* Key learning points

This approach ensures the lab is not only functional but also demonstrates a clear understanding of networking concepts.

---

## Configurations

Full device configurations can be found in the `configs/` directory.

---

## Notes

This lab is designed to reflect enterprise networking best practices at a CCNA level, with some elements extending slightly beyond to demonstrate deeper understanding.

In a production environment, additional features such as AAA (RADIUS/TACACS+), access control policies, and monitoring solutions would be implemented.
