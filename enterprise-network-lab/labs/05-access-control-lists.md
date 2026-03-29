# 05 - Access Control Lists (ACLs)

## Objective

The objective of this phase was to implement basic traffic filtering between branch networks using extended ACLs.

This demonstrates how routers can be used to enforce security policy and control communication between departments while still allowing access to shared HQ resources.

---

## Security Policy

A simple segmentation policy was implemented:

- Engineering users must not be able to communicate directly with Sales
- Sales users must not be able to communicate directly with Engineering
- Both branch networks must still be able to access HQ services and VLANs

### Networks

- Engineering: 192.168.30.0/24
- Sales: 192.168.40.0/24
- HQ Admin: 192.168.10.0/24
- HQ Servers: 192.168.20.0/24

---

## Design Approach

Extended ACLs were chosen because they allow filtering based on both source and destination addresses.

The ACLs were placed close to the source of the traffic, which is best practice for extended ACL deployment.

This prevents unwanted traffic from crossing the WAN unnecessarily.

---

## ACL Configuration

### BR1 - Block Engineering to Sales

```bash
ip access-list extended BLOCK_ENG_TO_SALES
 deny ip 192.168.30.0 0.0.0.255 192.168.40.0 0.0.0.255
 permit ip any any
```

Applied inbound on the LAN-facing interface:

```bash
interface gi0/0
 ip access-group BLOCK_ENG_TO_SALES in
```
---

## Block sales to Engineering 

```bash
ip access-list extended BLOCK_SALES_TO_ENG
 deny ip 192.168.40.0 0.0.0.255 192.168.30.0 0.0.0.255
 permit ip any any
```

Applied inbound on the LAN-facing interface:

```bash
interface gi0/0
 ip access-group BLOCK_SALES_TO_ENG in
```

---

## Why Inbound on the LAN Interface?

The ACLs were applied inbound on the branch LAN interfaces because:
- traffic is filtered as soon as it enters the router
- unnecessary traffic is stopped before it crosses the WAN
- this follows the best practice of placing extended ACLs close to the source

---

## Verification

The following tests were performed:

### Expected to Fail

From Engineering:
```bash
ping 192.168.40.10
```

<img width="875" height="325" alt="image" src="https://github.com/user-attachments/assets/1bd0f577-e52f-439f-8b53-46550be7a5a0" />

From Sales:
```bash
ping 192.168.30.10
```

<img width="875" height="273" alt="image" src="https://github.com/user-attachments/assets/0636a0d0-7760-40b9-869e-a339d9aaeb0a" />


### Expected to Succeed

From Engineering:

```bash
ping 192.168.10.10
ping 192.168.20.10
```

<img width="884" height="394" alt="image" src="https://github.com/user-attachments/assets/9ff6b704-f5e8-489f-8c99-6356f68c44ab" />


From Sales:

```bash
ping 192.168.10.10
ping 192.168.20.10
```

<img width="878" height="443" alt="image" src="https://github.com/user-attachments/assets/2a6f11dc-a889-417a-9571-d53c750c8413" />


---

## Verification Commands

```bash
show access-lists
show ip interface gi0/0
```

<img width="886" height="659" alt="image" src="https://github.com/user-attachments/assets/adefdb4c-79de-4cd1-8af7-80659d502f0a" />


### These commands confirm:

- ACL entries are present
- hit counts increase when traffic matches the rules
- the ACL is correctly applied to the interface

---

## Observations
- Inter-branch communication was successfully blocked
- Branch access to HQ resources remained functional
- ACL hit counters confirmed that the deny statements were matching traffic as expected

This demonstrates effective traffic segmentation using Layer 3 policy enforcement.

---

## Key Learning Points
- Standard ACLs filter only by source address, while extended ACLs filter by both source and destination
- Extended ACLs should generally be placed as close to the source as possible
- ACLs can be used to enforce simple departmental security boundaries
- Verification using hit counters is important to confirm that ACLs are working as intended

---

## Summary

Extended ACLs were successfully implemented on the branch routers to prevent direct communication between Engineering and Sales.
This improved the security posture of the network while maintaining required access to HQ services.
The lab now demonstrates not only switching and routing, but also basic policy enforcement through traffic filtering.

---
