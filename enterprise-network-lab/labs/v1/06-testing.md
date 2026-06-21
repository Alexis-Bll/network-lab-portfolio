# 06 - Testing and Validation

## Overview

This phase validates the completed V1 Enterprise Network Lab.

At this stage, the core switching, access layer, branch switches, OSPF routing, HSRP redundancy, ACLs, and Layer 2 security features have already been configured.

The purpose of this phase is to confirm that the network operates as intended under normal conditions and during simulated failure scenarios.

Testing focuses on:

- End-to-end connectivity
- OSPF route propagation
- ACL enforcement
- SSH management access
- HSRP failover
- General network resilience
- Known EVE-NG platform behaviour

---

## Objective

The objective of this phase is to prove that the completed V1 network is functional, resilient, and secure according to the intended design.

This includes verifying that:

- Permitted networks can communicate successfully
- Restricted traffic is blocked by ACLs
- OSPF neighbours form correctly
- OSPF routes are learned across the topology
- Devices can be managed using SSH
- HSRP provides gateway redundancy
- The network remains operational during simulated failover
- Any virtual platform limitations are documented clearly

---

## Topology Reference

The screenshot below shows the V1 EVE-NG topology.

![V1 EVE-NG Topology](../../topology/v1/v1-eve-ng-topology.png)

Testing was performed across the HQ site, both branch sites, and the simulated private WAN/MPLS-style provider network.

---

## Test Summary

| Test Area | Purpose | Result |
|---|---|---|
| End-to-end connectivity | Confirm permitted networks can communicate | Passed |
| ACL enforcement | Confirm Engineering and Sales are blocked from each other | Passed |
| SSH management | Confirm devices can be managed securely | Passed |
| HSRP failover | Confirm gateway redundancy works | Passed |
| OSPF validation | Confirm neighbour relationships and route learning | Passed |
| Platform behaviour | Document EVE-NG IOSvL2 management limitation | Documented |

---

# End-to-End Connectivity Testing

## Objective

The objective of the connectivity tests was to confirm that VLANs, routed links, OSPF, and ACL policies were working together correctly.

The tests validate that:

- HQ networks can reach required branch networks
- Branch networks can reach required HQ resources
- Engineering and Sales are blocked from communicating directly
- Routing and segmentation are working as intended

---

## Test Scenarios

### Permitted Traffic

| Source | Destination | Expected Result |
|---|---|---|
| HQ-R2 | Admin PC | Success |
| Admin PC | Server | Success |
| Admin PC | Engineering PC | Success |
| Admin PC | Sales PC | Success |
| Engineering PC | HQ Admin / Server networks | Success |
| Sales PC | HQ Admin / Server networks | Success |

### Restricted Traffic

| Source | Destination | Expected Result |
|---|---|---|
| Engineering PC | Sales PC | Fail |
| Sales PC | Engineering PC | Fail |

The restricted traffic is expected to fail because ACLs were configured in the previous phase to block direct Engineering-to-Sales and Sales-to-Engineering communication.

---

## Connectivity Test Evidence

### HQ-R2 to Admin PC

```cisco
ping 192.168.10.10
```

<img width="884" height="666" alt="HQ-R2 to Admin PC ping test" src="https://github.com/user-attachments/assets/6e5c70b0-d272-4132-b46e-008bf3983827" />

Result: Passed

---

### Admin PC to Server

```cisco
ping 192.168.20.10
```

<img width="883" height="229" alt="Admin PC to Server ping test" src="https://github.com/user-attachments/assets/4d7b5f62-8315-42aa-b427-64e7a252b16e" />

Result: Passed

---

### Admin PC to Engineering PC

```cisco
ping 192.168.30.10
```

<img width="885" height="191" alt="Admin PC to Engineering PC ping test" src="https://github.com/user-attachments/assets/9f83bdb4-5b75-4cfb-9224-8f4b6ebc2e34" />

Result: Passed

---

### Admin PC to Sales PC

```cisco
ping 192.168.40.10
```

<img width="884" height="237" alt="Admin PC to Sales PC ping test" src="https://github.com/user-attachments/assets/4e6c60a9-fe42-41ea-b3ca-619a5ab70b1d" />

Result: Passed

---

### Engineering PC to Sales PC

```cisco
ping 192.168.40.10
```

<img width="880" height="325" alt="Engineering PC to Sales PC blocked ping test" src="https://github.com/user-attachments/assets/5732823f-e1bf-4ab5-94f7-720aba383c65" />

Expected result: Fail

Result: Passed

The ping failed as expected because the ACL on BR1 blocks Engineering-to-Sales traffic.

---

### Sales PC to Engineering PC

```cisco
ping 192.168.30.10
```

<img width="880" height="225" alt="Sales PC to Engineering PC blocked ping test" src="https://github.com/user-attachments/assets/b283bf61-95bf-4b67-9f22-9207726ed892" />

Expected result: Fail

Result: Passed

The ping failed as expected because the ACL on BR2 blocks Sales-to-Engineering traffic.

---

## Connectivity Test Result

The connectivity tests confirmed that:

- Permitted traffic successfully reached its destination
- Engineering and Sales were blocked from communicating directly
- HQ resources remained accessible to required networks
- VLAN segmentation, OSPF routing, and ACL enforcement were working together correctly

---

# SSH Management Testing

## Objective

The objective of SSH testing was to confirm that network devices could be securely managed across the lab.

SSH was used instead of Telnet so that management traffic is encrypted.

Testing was performed between routers and switches because the lab endpoints do not natively provide full SSH client functionality.

---

## SSH Test Scenarios

| Test | Source | Destination | Expected Result |
|---|---|---|---|
| Access switch to core switch | HQ access switch | HQ-CSW1 loopback | Success |
| HQ router to core switch | HQ router | HQ-CSW1 loopback | Success |
| HQ router to branch router | HQ router | BR2 loopback | Success |
| HQ access switch to branch switch | HQ access switch | BR-SW1 management SVI | Success |

---

## SSH Test Evidence

### HQ Access Switch to Core Switch

```cisco
ssh 1.1.1.1
```

<img width="875" height="549" alt="SSH from HQ access switch to core switch" src="https://github.com/user-attachments/assets/d21cfbba-cbd2-4157-9980-a0508d24d3cb" />

Result: Passed

---

### HQ Router to Core Switch

```cisco
ssh 1.1.1.1
```

<img width="887" height="545" alt="SSH from HQ router to core switch" src="https://github.com/user-attachments/assets/ec8713a4-7d71-47f9-9997-5ceb85da5b16" />

Result: Passed

---

### HQ Router to Branch Router

```cisco
ssh 7.7.7.7
```

<img width="881" height="588" alt="SSH from HQ router to branch router" src="https://github.com/user-attachments/assets/0f575928-17c4-4442-bec2-37fa1d14801d" />

Result: Passed

In this lab, `7.7.7.7` is the loopback address of BR2.

---

### HQ Access Switch to Branch Switch

```cisco
ssh 192.168.30.2
```

<img width="881" height="532" alt="SSH from HQ access switch to branch switch" src="https://github.com/user-attachments/assets/97a2c5da-37d8-4f7d-86ee-8952247146bf" />

Result: Passed

---

## SSH Test Result

SSH testing confirmed that:

- Management access was available across the routed network
- Loopback interfaces provided stable management targets
- Access switches were reachable using management SVIs
- SSH was functioning across HQ and branch devices
- OSPF provided the required management reachability between sites

---

# HSRP Redundancy and Failover Testing

## Objective

The objective of HSRP testing was to confirm that HQ gateway redundancy worked correctly.

HSRP provides a virtual default gateway for HQ VLANs. If the active core switch fails, the standby core switch should take over the virtual gateway role.

---

## HSRP Design Tested

| VLAN | Virtual Gateway | Preferred Active Device | Standby Device |
|---|---|---|---|
| VLAN 10 | 192.168.10.1 | HQ-CSW1 | HQ-CSW2 |
| VLAN 20 | 192.168.20.1 | HQ-CSW2 | HQ-CSW1 |

The failover test focused on VLAN 10.

---

## HSRP Test Steps

### Step 1 - Confirm Initial HSRP State

```cisco
show standby brief
```

<img width="889" height="227" alt="Initial HSRP state" src="https://github.com/user-attachments/assets/89e03127-5d79-42bc-8c2b-9dbe72987893" />

Expected result:

- HQ-CSW1 should be active for VLAN 10
- HQ-CSW2 should be standby for VLAN 10

Result: Passed

---

### Step 2 - Simulate Failure on the Active Gateway

The active VLAN 10 SVI was shut down on HQ-CSW1.

```cisco
interface vlan 10
 shutdown
```

<img width="887" height="235" alt="HSRP failover shutdown command" src="https://github.com/user-attachments/assets/918a67fe-8f76-49e0-913c-a6734fe81198" />

---

### Step 3 - Confirm Standby Device Becomes Active

```cisco
show standby brief
```

<img width="881" height="199" alt="HSRP failover state after shutdown" src="https://github.com/user-attachments/assets/b747fec1-6c3a-4b4e-b0d8-8d860b906a24" />

<img width="891" height="194" alt="HSRP failover verification" src="https://github.com/user-attachments/assets/5675428f-3e9f-4118-bdb7-57ce5116276f" />

Expected result:

- HQ-CSW2 should become active for VLAN 10
- The virtual gateway should remain available

Result: Passed

---

### Step 4 - Confirm Gateway Reachability

```bash
ping 192.168.10.1
```

<img width="883" height="237" alt="HSRP virtual gateway ping test" src="https://github.com/user-attachments/assets/3e4beb20-a454-4973-bf83-19b78613d02e" />

Expected result:

- The virtual gateway remains reachable
- Minimal packet loss may occur during the failover event

Result: Passed

---

## HSRP Test Result

The HSRP failover test confirmed that:

- The standby core switch took over the active role
- The virtual gateway remained reachable
- End devices could continue using the same default gateway IP address
- Failover occurred without manual gateway reconfiguration
- The core layer provided gateway resilience for HQ VLANs

---

## HSRP Observation

During failover, the original active device entered an INIT state and displayed unknown HSRP roles due to the simulated loss of peer communication.

Despite this, the standby device successfully assumed the active role and the virtual IP address remained reachable.

This confirmed that HSRP was operating as intended in the lab.

---

# OSPF Routing Validation

## Objective

The objective of OSPF validation was to confirm that dynamic routing was functioning correctly across the V1 topology.

This included checking:

- OSPF neighbour relationships
- OSPF-learned routes
- VLAN route advertisement
- Branch route advertisement
- Loopback route advertisement
- End-to-end route propagation

---

## OSPF Validation Scenarios

| Test | Purpose | Expected Result |
|---|---|---|
| `show ip ospf neighbor` | Confirm OSPF adjacencies | Neighbours shown in FULL state |
| `show ip route` | Confirm route learning | Remote networks shown in routing table |
| Loopback reachability | Confirm loopbacks are advertised | Remote loopbacks reachable |
| Branch/HQ connectivity | Confirm end-to-end routing | Required networks reachable |

---

## OSPF Neighbour Verification

```cisco
show ip ospf neighbor
```

<img width="886" height="249" alt="show ip ospf neighbor output" src="https://github.com/user-attachments/assets/210d718f-2502-48c6-86cb-1a1ecce2ef4a" />

<img width="888" height="171" alt="show ip ospf neighbor output" src="https://github.com/user-attachments/assets/80b96972-4537-4da4-8053-de4f09f9ce00" />

Expected result:

- OSPF neighbours should form across routed infrastructure links
- Neighbours should reach the FULL state

Result: Passed

---

## Routing Table Verification

```cisco
show ip route
```

<img width="805" height="837" alt="show ip route output" src="https://github.com/user-attachments/assets/b123a10b-799b-4e24-b9d2-136093e9988f" />

<img width="787" height="831" alt="show ip route output" src="https://github.com/user-attachments/assets/190c92cd-cf9b-4f4d-b0ba-f08f4a5953b7" />

Expected result:

- HQ VLAN routes are present
- Branch LAN routes are present
- WAN transit routes are present
- Loopback routes are present

Result: Passed

---

## OSPF Test Result

OSPF validation confirmed that:

- OSPF adjacencies formed successfully
- Dynamic route learning was working
- VLAN and branch networks were present in routing tables
- Loopbacks were advertised and reachable
- Static routes were not required for internal V1 connectivity
- OSPF provided routed connectivity across HQ, WAN, ISP, and branch devices

---

# Known Behaviour / EVE-NG Platform Limitation

## IOSvL2 Management Reachability

During testing, an issue was observed where some IOSvL2 switches did not consistently reach remote subnets using only the `ip default-gateway` command.

In a normal Layer 2 switch management scenario, `ip default-gateway` is typically sufficient when `ip routing` is disabled.

However, in this virtual EVE-NG lab environment, management traffic from some IOSvL2 images required an additional workaround to behave consistently.

---

## Workaround Used

To support reliable management connectivity in the virtual lab, the following workaround was used where required:

```cisco
ip routing
ip route 0.0.0.0 0.0.0.0 <gateway>
```

Example:

```cisco
ip routing
ip route 0.0.0.0 0.0.0.0 192.168.30.1
```

---

## Why This Was Documented

This behaviour was documented because it affected management-plane testing in the virtual lab.

The workaround:

- Improved SSH and ICMP reachability from affected virtual switches
- Supported testing across routed subnets
- Did not change the intended Layer 2 access-switch role for user traffic
- Was specific to the lab environment and virtual image behaviour

---

## Production Note

In a production environment, this workaround would not normally be required for a Layer 2 access switch.

A traditional Layer 2 management configuration would usually use:

```cisco
ip default-gateway <gateway>
```

This note is included to make the lab behaviour transparent and to explain the difference between intended design and virtual platform behaviour.

---

# Final Validation Summary

## Passed Validation Areas

| Area | Validation Result |
|---|---|
| HQ VLAN connectivity | Passed |
| Branch connectivity | Passed |
| Inter-VLAN routing | Passed |
| OSPF neighbour formation | Passed |
| OSPF route propagation | Passed |
| ACL enforcement | Passed |
| SSH management access | Passed |
| HSRP failover | Passed |
| Gateway reachability after failover | Passed |
| EVE-NG platform limitation documented | Passed |

---

## Expected Results Confirmed

The completed V1 network successfully demonstrated:

- A working private multi-site enterprise WAN
- HQ VLAN segmentation
- Branch LAN connectivity
- Inter-VLAN routing at the HQ core
- Dynamic routing using OSPF
- Gateway redundancy using HSRP
- ACL-based segmentation between Engineering and Sales
- SSH-based management reachability
- Resilience during simulated gateway failover

---

## Outcome

The V1 Enterprise Network Lab was successfully validated.

Testing confirmed that the design operates correctly under normal conditions and during simulated failover.

The network provides:

- Reliable internal connectivity
- Dynamic route learning
- Controlled departmental segmentation
- Secure device management
- Gateway redundancy
- Documented troubleshooting and platform behaviour

This confirms that V1 provides a stable and operational enterprise network foundation.

---

## Key Learning

This phase reinforced the importance of validating a network after configuration is complete.

Key learning points:

- Configuration alone does not prove a network works
- End-to-end testing confirms whether the design behaves as intended
- ACLs should be tested for both permitted and denied traffic
- OSPF should be validated using both neighbour and routing table checks
- HSRP failover should be tested rather than assumed
- SSH testing confirms management-plane reachability
- Virtual lab platforms can behave differently from physical hardware
- Clear documentation of testing evidence improves the credibility of the lab

---

## Related Documentation

- [Previous: 05 - Access Control Lists](05-access-control-lists.md)
- [V1 Overview](./)
- [V1 Topology](../../topology/v1/)
- [Device Configurations](../../configs/)
- [Enterprise Network Lab Overview](../../)

---
