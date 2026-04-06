# 06 - Testing and Validation

## Objective

The objective of this phase was to validate the overall functionality, resilience, and management of the enterprise network.

Testing was performed to ensure that:

- End-to-end connectivity is functioning correctly across all sites
- Security policies (ACLs) are enforced as intended
- Redundancy mechanisms provide failover without loss of connectivity
- Network devices are accessible via secure management protocols (SSH)

This phase confirms that the network is not only correctly configured, but also operates reliably under normal and failure conditions.

---

## End-to-End Connectivity Testing

### Objective

To verify that all network segments can communicate correctly across the enterprise network following the implementation of VLANs, routing, and ACL policies.

---

### Test Scenarios

The following connectivity scenarios were tested:

#### 1. Branch to HQ

- HQ-R2 → HQ Admin VLAN (192.168.10.0/24)
- HQ Admin VLAN → HQ Server VLAN (192.168.20.0/24)
- HQ Admin VLAN → Engineering VLAN (192.168.30.0/24)
- HQ Admin VLAN → Sales VLAN (192.168.40.0/24)

#### 2. Inter-Branch Communication

- Engineering → Sales ❌ (expected to fail due to ACL)
- Sales → Engineering ❌ (expected to fail due to ACL)

---

### Commands Used

#### Ping from HQ-R2 to Admin 1

- ```ping 192.168.10.10```
<img width="884" height="666" alt="image" src="https://github.com/user-attachments/assets/6e5c70b0-d272-4132-b46e-008bf3983827" />

---

#### Ping from Admin 1 to Server 1

- ```ping 192.168.20.10```
<img width="883" height="229" alt="image" src="https://github.com/user-attachments/assets/4d7b5f62-8315-42aa-b427-64e7a252b16e" />

---

#### Ping from Admin 1 to Engineering PC

- ```ping 192.168.30.10```
<img width="885" height="191" alt="image" src="https://github.com/user-attachments/assets/9f83bdb4-5b75-4cfb-9224-8f4b6ebc2e34" />

---

#### Ping from Admin 1 to Sales PC

- ```ping 192.168.40.10```
<img width="884" height="237" alt="image" src="https://github.com/user-attachments/assets/4e6c60a9-fe42-41ea-b3ca-619a5ab70b1d" />

---

#### Ping from Engineering to Sales PC

- ```ping 192.168.40.10```
<img width="880" height="325" alt="image" src="https://github.com/user-attachments/assets/5732823f-e1bf-4ab5-94f7-720aba383c65" />

---

#### Ping from Sales to Engineering PC

- ```ping 192.168.40.10```
<img width="880" height="225" alt="image" src="https://github.com/user-attachments/assets/b283bf61-95bf-4b67-9f22-9207726ed892" />

---

### Result

- All permitted traffic successfully reached its destination
- Inter-branch communication was correctly blocked by ACL policies
- Confirms correct operation of VLAN segmentation, OSPF routing, and ACL enforcement

---

## Management Access (SSH Testing)

### Objective

To verify that all network devices can be securely accessed using SSH, ensuring proper management connectivity across the enterprise network.

---

### Test Scenarios

The following management access scenarios were tested:

#### 1. Access Switch to Core

- HQ Access Switch → Core Switch (SVI / Loopback)

#### 2. Router to Router

- HQ Router → Branch Router (Loopback)
- Branch Router → HQ Router (Loopback)

#### 3. End Device (HQ Access Switch to Network Devices)

- HQ Access Switch → Branch Switch (Management SVI)

Note:
- SSH testing was performed between network devices (routers and switches), as end hosts in this lab environment do not natively support SSH clients.
- This reflects real-world scenarios where network devices are commonly managed from other infrastructure devices or dedicated management workstations.

---

### Commands Used

#### ssh from HQ-access to Core

- ```ssh 1.1.1.1```
<img width="875" height="549" alt="image" src="https://github.com/user-attachments/assets/d21cfbba-cbd2-4157-9980-a0508d24d3cb" />

---

#### ssh from HQ Router to Core

- ```ssh 1.1.1.1```
<img width="887" height="545" alt="image" src="https://github.com/user-attachments/assets/ec8713a4-7d71-47f9-9997-5ceb85da5b16" />

---

#### ssh from HQ Router to Branch Router

- ```ssh 7.7.7.7```
<img width="881" height="588" alt="image" src="https://github.com/user-attachments/assets/0f575928-17c4-4442-bec2-37fa1d14801d" />

---

#### ssh HQ Access Switch to Branch Switch

- ```ssh 192.168.30.2```
<img width="881" height="532" alt="image" src="https://github.com/user-attachments/assets/97a2c5da-37d8-4f7d-86ee-8952247146bf" />

---

### Result

- SSH connectivity was successfully established across all devices
- Loopback interfaces provided stable and consistent management endpoints
- Access switches were reachable via their management SVIs
- Confirms correct implementation of the management plane across the network

---

## Redundancy and Failover Testing (HSRP)

### Objective

To verify that gateway redundancy is functioning correctly using HSRP, ensuring continuous network availability in the event of a core switch failure.

---

### Test Scenario

HSRP was configured on the core switches to provide a virtual default gateway for end devices.

A failover test was performed by simulating a failure of the active HSRP device.

---

### Test Steps

#### 1. Identify the active and standby devices:

```cisco
show standby brief
```

<img width="889" height="227" alt="image" src="https://github.com/user-attachments/assets/89e03127-5d79-42bc-8c2b-9dbe72987893" />

---


#### 2. Simulate failure on the active device (HQ-CSW1):

```cisco
interface vlan 10
shutdown
```

<img width="887" height="235" alt="image" src="https://github.com/user-attachments/assets/918a67fe-8f76-49e0-913c-a6734fe81198" />

---


#### 3. Verify that the standby device (HQ-CSW2) becomes active:

```cisco
show standby brief
```

<img width="881" height="199" alt="image" src="https://github.com/user-attachments/assets/b747fec1-6c3a-4b4e-b0d8-8d860b906a24" />

<img width="891" height="194" alt="image" src="https://github.com/user-attachments/assets/5675428f-3e9f-4118-bdb7-57ce5116276f" />

---

#### 4. Test connectivity from an end device:

```bash
ping 192.168.10.1
```

<img width="883" height="237" alt="image" src="https://github.com/user-attachments/assets/3e4beb20-a454-4973-bf83-19b78613d02e" />

---

### Expected Behaviour

- The standby device should transition to the active state.
- The virtual IP (192.168.10.1) should remain reachable.
- Minimal packet loss may occur during failover.

---

### Result

- HSRP failover occurred successfully.
- The standby core switch assumed the active role.
- End devices maintained connectivity to the default gateway.
- Traffic continued to flow with minimal disruption.

---

### Observations

- HSRP provided seamless gateway redundancy
- The virtual IP remained consistent during failover
- Network resilience was maintained without manual intervention
- During failover, the original active device entered an INIT state and displayed unknown HSRP roles due to loss of peer communication. Despite this, the standby device successfully assumed the active role and the virtual IP remained reachable.

This confirms correct implementation of redundancy at the core layer.

---

## OSPF Routing Validation

### Objective

To verify that OSPF is correctly establishing adjacencies and advertising all required networks across the enterprise topology.

---

### Test Scenarios

- Verify OSPF neighbour relationships between routers
- Confirm that all VLAN and loopback networks are present in the routing table
- Validate end-to-end route propagation between branch and HQ

---

### Commands Used

#### To verify that OSPF is establishing neighbour adjacencies

```show ip ospf neighbor```

<img width="886" height="249" alt="image" src="https://github.com/user-attachments/assets/210d718f-2502-48c6-86cb-1a1ecce2ef4a" />

<img width="888" height="171" alt="image" src="https://github.com/user-attachments/assets/80b96972-4537-4da4-8053-de4f09f9ce00" />

---

#### To verify that OSPF is populating the routing table with dynamically learned routes.

```show ip route```

<img width="805" height="837" alt="image" src="https://github.com/user-attachments/assets/b123a10b-799b-4e24-b9d2-136093e9988f" />

<img width="787" height="831" alt="image" src="https://github.com/user-attachments/assets/190c92cd-cf9b-4f4d-b0ba-f08f4a5953b7" />

---

### Result

- OSPF adjacencies were successfully formed between all routers
- All VLAN networks (192.168.x.x) were present in the routing tables
- Loopback interfaces were advertised and reachable across the network
- Confirms correct dynamic routing configuration across the topology

---

### Observations

- OSPF provided fast and consistent route propagation between all sites
- The use of loopback interfaces ensured stable router identification
- Dynamic routing removed the need for manual route configuration

---

## Known Behaviour / Platform Limitation (IOSvL2)

During testing, an issue was observed where Layer 2 switches were unable to reach remote subnets using only the `ip default-gateway` command.

Although this behaviour would normally be sufficient on real hardware, the IOSvL2 images used in EVE-NG did not consistently forward management traffic to remote networks.

---

### Workaround Implemented

To ensure reliable management connectivity, the following configuration was applied:

```cisco
ip routing
ip route 0.0.0.0 0.0.0.0 <gateway>
```

### Example:

```cisco
ip routing
ip route 0.0.0.0 0.0.0.0 192.168.30.1
```

---

### Justification

- Enables the switch to reach remote subnets for SSH and ICMP
- Does not impact Layer 2 switching behaviour for user traffic
- Applied only to support lab environment limitations

---

### Note

In production environments, this behaviour would typically not be required, as Layer 2 switches correctly utilise the ip default-gateway for management traffic.

---

## Summary

The network was successfully validated across all key areas, including connectivity, security, redundancy, and management.

Testing confirmed that the design operates reliably under both normal conditions and simulated failure scenarios, demonstrating a resilient and scalable enterprise network implementation.

---

