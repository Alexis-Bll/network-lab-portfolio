# Device Configurations

This folder contains the device configuration files for the **Enterprise Network Lab**.

The main configuration files in this directory represent the completed **V1 network baseline**. These are the cleaned router and switch configurations used to document the working state of the original enterprise network lab.

V2 configuration changes are documented separately in the [`changes-v2/`](changes-v2/) folder.

---

## Configuration Structure

| Location | Purpose |
|---|---|
| Main `configs/` folder | Full cleaned device configurations for the completed V1 lab |
| [`changes-v2/`](changes-v2/) | Configuration snippets and notes for V2 enhancements |

This structure keeps the completed V1 baseline separate from the additional V2 changes.

---

## V1 Configuration Status

### Completed

The V1 network has been configured, tested, and documented.

The configurations support:

- End-to-end connectivity across all sites
- VLAN segmentation
- Inter-VLAN routing
- OSPF route propagation
- HSRP gateway redundancy
- EtherChannel between core switches
- ACL enforcement between departments
- Layer 2 security features
- SSH management access

These files represent the working V1 configuration state of the lab.

---

## Device Configuration Files

### WAN / Edge Devices

| Device | Role | Configuration |
|---|---|---|
| ISP | Simulated WAN/MPLS provider router | [ISP.txt](ISP.txt) |
| HQ-R1 | HQ WAN router | [HQ-R1.txt](HQ-R1.txt) |
| HQ-R2 | HQ WAN router | [HQ-R2.txt](HQ-R2.txt) |
| BR1 | Engineering branch router | [BR1.txt](BR1.txt) |
| BR2 | Sales branch router | [BR2.txt](BR2.txt) |

### HQ Core Layer

| Device | Role | Configuration |
|---|---|---|
| HQ-CSW1 | HQ core switch 1 | [HQ-CSW1.txt](HQ-CSW1.txt) |
| HQ-CSW2 | HQ core switch 2 | [HQ-CSW2.txt](HQ-CSW2.txt) |

### HQ Access Layer

| Device | Role | Configuration |
|---|---|---|
| HQ-ASW1 | Admin access switch 1 | [HQ-ASW1.txt](HQ-ASW1.txt) |
| HQ-ASW2 | Admin access switch 2 | [HQ-ASW2.txt](HQ-ASW2.txt) |
| HQ-SSW1 | Server access switch 1 | [HQ-SSW1.txt](HQ-SSW1.txt) |
| HQ-SSW2 | Server access switch 2 | [HQ-SSW2.txt](HQ-SSW2.txt) |

### Branch Access Layer

| Device | Role | Configuration |
|---|---|---|
| BR-SW1 | Engineering branch switch | [BR-SW1.txt](BR-SW1.txt) |
| BR-SW2 | Sales branch switch | [BR-SW2.txt](BR-SW2.txt) |

---

## Technologies Implemented in V1

The V1 configurations demonstrate:

- VLAN segmentation
- Inter-VLAN routing using SVIs
- OSPF dynamic routing across a multi-site network
- HSRP gateway redundancy and load sharing
- LACP EtherChannel between the HQ core switches
- Rapid PVST+ with root bridge tuning
- Access Control Lists for department-level traffic filtering
- DHCP Snooping
- Dynamic ARP Inspection
- Port Security
- PortFast and BPDU Guard
- SSH-based management access

---

## Configuration Approach

The configuration files have been cleaned and structured for documentation and review.

This means they are intended to:

- Highlight relevant configuration lines
- Remove unnecessary default or auto-generated Cisco output
- Make the lab easier to review
- Keep naming and formatting consistent across devices
- Reflect the final working V1 baseline

This approach makes the files more useful for technical review, handover, and portfolio presentation.

---

## V2 Configuration Changes

V2 builds on the V1 baseline rather than replacing it.

The V2 changes are documented separately in:

- [`changes-v2/`](changes-v2/)

V2 introduces additional functionality such as:

- NAT/PAT at the HQ edge
- Internet access for internal VLANs
- Centralised DHCP services
- DNS and Active Directory services
- Application server integration
- Minor routing updates to support external connectivity

---

## Related Documentation

- [Enterprise Network Lab Overview](../)
- [V1 Lab Documentation](../labs/v1/)
- [V2 Lab Documentation](../labs/v2/)
- [Topology Files](../topology/)

---
