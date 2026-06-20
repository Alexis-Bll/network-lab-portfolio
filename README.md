# Network Lab Portfolio

This repository showcases my hands-on networking and infrastructure lab work, focused on building practical skills in enterprise network design, implementation, troubleshooting, and documentation.

The portfolio is centred around an **Enterprise Network Lab** built in **EVE-NG**, with two defined stages:

- **V1** — a completed CCNA-level enterprise network foundation.
- **V2** — an enterprise services expansion adding internet access, NAT, DHCP, DNS, Active Directory, Group Policy, and application server integration.

The aim of this portfolio is to demonstrate my ability to **design, configure, test, troubleshoot, and document enterprise-style network environments** using industry-relevant technologies.

---

## About Me

I am a UK-based **Junior IT Support and Network Engineer** working for an **MSP**, with a **First Class degree in Forensic Computing & Security from Bournemouth University**.

I have achieved the **Cisco CCNA certification** and built this portfolio to develop and demonstrate practical networking, infrastructure, and troubleshooting skills.

I am continuing to expand my knowledge in enterprise networking, network security, firewalls, and MSP-style support environments, with a particular interest in **Fortinet technologies**.

---

## What This Portfolio Demonstrates

This portfolio highlights my ability to design, configure, validate, and document realistic network environments, including:

- Multi-site enterprise network design
- VLAN segmentation and inter-VLAN routing
- Core/access switching design
- WAN routing between HQ and branch sites
- Dynamic routing using OSPF
- High availability using HSRP
- Spanning Tree tuning and Layer 2 loop prevention
- EtherChannel using LACP
- Access Control Lists for traffic restriction
- Layer 2 security controls including DHCP Snooping, Dynamic ARP Inspection, Port Security, and BPDU Guard
- Internet edge connectivity using NAT/PAT
- Centralised DHCP and DNS services
- Active Directory Domain Services and Group Policy
- End-to-end validation, failover testing, and troubleshooting documentation

All labs are built and tested in a virtual environment to simulate real-world enterprise scenarios.

---

## Lab Environment

The lab environment uses:

- **EVE-NG** for network simulation
- **Cisco virtual routers and switches**
- **Virtual PCs and servers**
- **Windows Server** for DHCP, DNS, Active Directory, and Group Policy in V2

This allows realistic testing of routing, switching, redundancy, segmentation, security controls, and enterprise services.

---

## Featured Project

### Enterprise Network Lab

The main project in this portfolio is a multi-site enterprise network lab.

| Version | Status | Focus |
|---|---|---|
| **V1** | Completed | CCNA-level enterprise network foundation including VLANs, HSRP, STP, EtherChannel, OSPF, ACLs, and Layer 2 security |
| **V2** | In Progress | Enterprise services expansion including internet access, NAT, DHCP, DNS, Active Directory, Group Policy, and application server integration |

---

## Enterprise Network Lab - V1

**V1** is the completed foundation of the lab.

It demonstrates core networking skills including:

- HQ and branch network design
- Department-based VLAN segmentation
- Dual core switch design
- HSRP default gateway redundancy
- Rapid PVST+ tuning and root bridge control
- LACP EtherChannel between core switches
- OSPF routing across HQ, ISP, and branch routers
- ACLs to restrict Engineering and Sales traffic
- Layer 2 security using DHCP Snooping, Dynamic ARP Inspection, Port Security, and BPDU Guard
- SSH management access
- End-to-end testing and validation

Key V1 links:

- [V1 Lab Documentation](enterprise-network-lab/labs/v1/)
- [V1 Topology](enterprise-network-lab/topology/v1/)
- [Device Configurations](enterprise-network-lab/configs/)

---

## Enterprise Network Lab - V2

**V2** expands the original network by adding services commonly found in enterprise environments.

It introduces:

- Internet edge connectivity
- NAT/PAT on the HQ edge router
- Default route advertisement into OSPF
- Centralised DHCP services from Windows Server
- DHCP relay using `ip helper-address`
- DNS services using Active Directory-integrated DNS
- Active Directory Domain Services
- Domain-joined clients across multiple VLANs
- Organisational Units, users, groups, and permissions
- Group Policy testing
- Application server integration

V2 is currently partially complete and will be finished after V1 has been fully reviewed, polished, and confirmed operational.

Key V2 links:

- [V2 Lab Documentation](enterprise-network-lab/labs/v2/)
- [V2 Topology](enterprise-network-lab/topology/v2/)
- [V2 Configuration Changes](enterprise-network-lab/configs/changes-v2/)

---

## Key Technologies

Technologies and concepts demonstrated across the portfolio include:

- VLANs and trunking
- Layer 2 switching
- Inter-VLAN routing
- OSPF
- HSRP
- Rapid PVST+
- EtherChannel / LACP
- ACLs
- DHCP Snooping
- Dynamic ARP Inspection
- Port Security
- BPDU Guard
- WAN point-to-point links
- NAT/PAT
- DHCP relay
- DNS
- Active Directory Domain Services
- Group Policy

---

## Career Development

This portfolio was originally created to support my transition into my first IT/networking role.

I have now secured a role as a **Junior IT Support and Network Engineer for an MSP**, where I am continuing to build real-world experience supporting business networks, infrastructure, and client environments.

My current goal is to keep developing as a network engineer by combining hands-on workplace experience with structured lab work, technical documentation, and continued study in network security and firewall technologies.

---
