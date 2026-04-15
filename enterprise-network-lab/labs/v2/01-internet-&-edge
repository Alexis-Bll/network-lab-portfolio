# 01 - Internet Edge & NAT

## Objective

Extend the existing enterprise network by introducing external connectivity and Network Address Translation (NAT), allowing internal devices to communicate with external networks.

---

## Overview

In this phase, HQ-R1 is configured as the internet edge router. It provides connectivity between the internal enterprise network and an external network (simulated internet).

Port Address Translation (PAT) is implemented to allow multiple internal devices using private IP addressing to share a single public IP address when accessing external resources.

---

## Topology Context

* **HQ-R1** acts as the NAT boundary
* Internal networks use **192.168.0.0/16**
* Transit/WAN links use **10.0.x.x** (not translated)
* External network connected via **GigabitEthernet0/3**

---

## Key Concepts Implemented

* NAT (PAT / Overload)
* Inside and Outside interface designation
* Access control for NAT traffic
* Default route configuration
* OSPF default route advertisement

---

## Configuration Summary

### Interface Roles

| Interface | Role                   |
| --------- | ---------------------- |
| Gi0/0     | NAT Inside             |
| Gi0/1     | NAT Inside             |
| Gi0/2     | NAT Inside             |
| Gi0/3     | NAT Outside (Internet) |

---

### NAT Configuration

```bash
access-list 1 permit 192.168.0.0 0.0.255.255

ip nat inside source list 1 interface GigabitEthernet0/3 overload
```

---

### Interface Configuration

```bash
interface GigabitEthernet0/0
 ip nat inside

interface GigabitEthernet0/1
 ip nat inside

interface GigabitEthernet0/2
 ip nat inside

interface GigabitEthernet0/3
 description TO INTERNET
 ip address 203.0.113.2 255.255.255.252
 ip nat outside
 no shutdown
```

---

### Default Route

```bash
ip route 0.0.0.0 0.0.0.0 203.0.113.1
```

---

### OSPF Default Route Advertisement

```bash
router ospf 1
 default-information originate
```

---

## Verification

### NAT Translations

```bash
show ip nat translations
```

Example output:

```
Inside local     Inside global     Outside local     Outside global
192.168.10.10    203.0.113.2       8.8.8.8           8.8.8.8
```

---

### Connectivity Test

From an internal client:

```bash
ping 8.8.8.8
```

Result:

* Traffic is successfully translated and forwarded
* ICMP replies are not received due to the simulated nature of the internet environment

---

## Observations

* NAT translations are successfully created on HQ-R1
* Internal private addresses are correctly translated to the public interface address
* Traffic reaches the external network, confirming correct routing and NAT operation
* Lack of reply traffic is expected, as the lab does not include a fully functional internet simulation

---

## Design Considerations

* Only **192.168.0.0/16** networks are translated
* **10.0.x.x transit networks are excluded** to reflect real-world best practices
* PAT is used to conserve public IP space
* HQ-R1 acts as the single internet egress point

---

## Outcome

The enterprise network is now capable of sending traffic to external networks through a properly configured NAT boundary, representing a realistic internet edge deployment.

---
