# 03 - Routing (OSPF Multi-Site Connectivity)

## Objective

The objective of this phase was to implement dynamic routing across the enterprise network to enable full end-to-end connectivity between:

- HQ VLANs (Admin and Servers)
- Branch office networks
- Core and edge routing devices

OSPF (Open Shortest Path First) was chosen as the routing protocol to provide scalable, efficient, and automatic route propagation across the network.

---

## Network Design Overview

The network is designed using a hierarchical enterprise model:

- Core Layer (Layer 3 switches at HQ)
- Distribution/Edge Layer (HQ routers)
- WAN/Core Transport (simulated ISP)
- Branch Routers
- Access Layer (end devices and servers)

All devices participate in a single OSPF Area (Area 0) to simplify design and ensure full route visibility.

---

## IP Addressing Strategy

The network uses private IP addressing (RFC1918) throughout:

### LAN Networks
- VLAN 10 (Admin): 192.168.10.0/24
- VLAN 20 (Servers): 192.168.20.0/24
- Branch 1: 192.168.30.0/24
- Branch 2: 192.168.40.0/24

### WAN / Transit Networks
- 10.0.0.0/30 → ISP ↔ HQ / Branch links
- 10.0.1.0/30 → Core ↔ HQ Router links

---

### Why private IP addressing?

This design simulates a real-world enterprise WAN or MPLS environment where:

- Internal communication uses private addressing
- The service provider (ISP/MPLS core) transports traffic between sites
- No public IP addresses are required internally

This approach reflects how modern enterprise networks operate, where:
- Routing is handled internally using protocols like OSPF
- NAT is only required at the internet edge (not implemented in this lab)

---

### Why /30 subnets?

/30 networks were used for point-to-point links because:

- Only 2 usable IP addresses are required per link
- Efficient use of address space
- Standard best practice for WAN links

---

## OSPF Configuration

OSPF was configured on all Layer 3 devices using Process ID 1.

All devices were placed into Area 0 (backbone area).

Each device was assigned a unique router ID for stability and troubleshooting.

---

### Example Configuration (HQ-CSW1)

```cisco
router ospf 1
 router-id 1.1.1.1
 network 1.1.1.1 0.0.0.0 area 0
 network 192.168.10.0 0.0.0.255 area 0
 network 192.168.20.0 0.0.0.255 area 0
 network 10.0.1.0 0.0.0.3 area 0
 network 10.0.1.8 0.0.0.3 area 0
```

---

## Loopback Interfaces and OSPF Advertisement

Loopback interfaces were configured on all Layer 3 devices to provide a stable management address and consistent router identity.

Unlike physical interfaces, loopbacks remain up as long as the device is operational, making them well suited for SSH management and OSPF router IDs.

To ensure reachability across the entire enterprise network, each loopback was advertised into OSPF as a /32 route in Area 0.

This allows routers and core switches to be managed using stable loopback addresses rather than relying only on physical or SVI addresses.

---

### Loopback Addressing

- HQ-CSW1 → 1.1.1.1/32
- HQ-CSW2 → 2.2.2.2/32
- HQ-R1 → 3.3.3.3/32
- HQ-R2 → 4.4.4.4/32
- BR1 → 5.5.5.5/32
- BR2 → 6.6.6.6/32
- ISP → 7.7.7.7/32

---

### Example Configuration (HQ-R1)

```cisco
interface loopback0
 ip address 3.3.3.3 255.255.255.255

router ospf 1
 router-id 3.3.3.3
 network 3.3.3.3 0.0.0.0 area 0
 passive-interface loopback0
```

---

### Example Configuration (HQ-CSW1)

```cisco 
interface loopback0
 ip address 1.1.1.1 255.255.255.255

router ospf 1
 router-id 1.1.1.1
 network 1.1.1.1 0.0.0.0 area 0
 passive-interface loopback0
 passive-interface vlan 10
 passive-interface vlan 20
 ```

## Passive Interfaces

To improve efficiency and security, OSPF was not enabled on user-facing interfaces:

```cisco
router ospf 1
 passive-interface vlan 10
 passive-interface vlan 20
 passive-interface loopback0
```

This prevents unnecessary OSPF neighbor formation on access networks while still advertising the networks.

note:
- passive-interface loopback0 was applied to all routers and core switches.
- passive-interface vlan 10 & vlan 20 was applied to core switches only.

---

### Design Justification
- Provides stable IP reachability for management access
- Ensures router IDs remain consistent even if physical interfaces change
- Advertises loopbacks as /32 routes across the network
- Aligns with enterprise best practices for management and routing design

---

## Routing Behaviour

Once OSPF was enabled:

- All routers dynamically learned remote networks
- Manual static routes were no longer required
- The network converged automatically

### Example:

- Branch routers learned HQ VLANs (192.168.10.0/24, 192.168.20.0/24)
- HQ core switches learned branch networks (192.168.30.0/24, 192.168.40.0/24)
- ISP router acted as a transit network between sites

---

### Redundancy

The network includes:

- Dual HQ routers
- Dual core switches
- Multiple equal-cost paths

OSPF automatically performs load balancing across equal-cost paths (ECMP).

---

## Verification

The following commands were used to verify correct operation:

- ```show ip ospf neighbor```
<img width="883" height="212" alt="image" src="https://github.com/user-attachments/assets/123aa4eb-bace-4418-8207-0f228cfc4a5d" />

- ```show ip route```
<img width="888" height="668" alt="image" src="https://github.com/user-attachments/assets/3d84814d-5c20-416d-bbeb-816b4203236b" />

- ```show ip protocols```
<img width="883" height="672" alt="image" src="https://github.com/user-attachments/assets/678169b8-35c1-4fdf-9a5c-9decdf494c58" />

---

## Successful Tests

- HQ devices can reach both branch networks
- Branch devices can reach HQ VLANs
- Servers and admin PCs can communicate across sites

### Example:

```cisco
ping 192.168.30.1
ping 192.168.40.1
```
<img width="883" height="398" alt="image" src="https://github.com/user-attachments/assets/d1f92e4c-b97d-4933-8aae-3a9f47c1c818" />


---

## Observations
- OSPF routes appear with "O" in the routing table
- Multiple routes exist for redundancy
- Convergence occurs automatically after link changes

---

## Key Learning Points

- OSPF enables scalable dynamic routing in enterprise networks
- Private IP addressing is standard for internal enterprise and MPLS networks
- /30 subnets are efficient for point-to-point links
- Passive interfaces improve security and reduce unnecessary traffic
- Redundant paths allow OSPF to provide automatic failover and load balancing
- Layer 3 switching enables inter-VLAN routing at the core layer

## Summary

In this phase, full end-to-end routing was achieved across the enterprise network using OSPF.

All sites are now interconnected, and the network can dynamically adapt to changes without manual intervention.

This completes the core enterprise routing design and prepares the network for future enhancements such as:

- NAT and internet access
- Advanced routing (EIGRP/BGP)
- Network services (DHCP, DNS, Active Directory)

---
