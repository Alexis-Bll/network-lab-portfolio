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

- Engineering PC → HQ Admin VLAN (192.168.10.0/24)
- Engineering PC → HQ Server VLAN (192.168.20.0/24)
- Sales PC → HQ Admin VLAN (192.168.10.0/24)
- Sales PC → HQ Server VLAN (192.168.20.0/24)

#### 2. Inter-Branch Communication

- Engineering → Sales ❌ (expected to fail due to ACL)
- Sales → Engineering ❌ (expected to fail due to ACL)

---

### Commands Used

#### Ping from HQ-R2 to Admin 1

- ```ping 192.168.10.10```
<img width="884" height="666" alt="image" src="https://github.com/user-attachments/assets/6e5c70b0-d272-4132-b46e-008bf3983827" />

#### Ping from Admin 1 to Server 1

- ```ping 192.168.20.10```
<img width="883" height="229" alt="image" src="https://github.com/user-attachments/assets/4d7b5f62-8315-42aa-b427-64e7a252b16e" />

#### Ping from Admin 1 to Engineering PC

- ```ping 192.168.30.10```
<img width="885" height="191" alt="image" src="https://github.com/user-attachments/assets/9f83bdb4-5b75-4cfb-9224-8f4b6ebc2e34" />

#### Ping from Admin 1 to Sales PC

- ```ping 192.168.40.10```
<img width="884" height="237" alt="image" src="https://github.com/user-attachments/assets/4e6c60a9-fe42-41ea-b3ca-619a5ab70b1d" />

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

### example:

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

