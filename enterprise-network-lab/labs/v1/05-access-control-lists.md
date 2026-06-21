# 05 - Access Control Lists

## Overview

This phase configures Access Control Lists for the V1 Enterprise Network Lab.

At this stage, OSPF routing is already operational and all internal networks can reach each other. The purpose of this phase is to introduce Layer 3 traffic filtering so that departmental networks are segmented according to a basic security policy.

The ACLs are used to prevent direct communication between the Engineering and Sales branch networks while still allowing both departments to access required HQ resources.

---

## Objective

The objective of this phase is to enforce controlled traffic filtering between branch departments.

This includes:

- Creating extended ACLs on the branch routers
- Blocking Engineering-to-Sales traffic
- Blocking Sales-to-Engineering traffic
- Allowing both departments to access HQ Admin and Server VLANs
- Applying ACLs close to the source of traffic
- Verifying ACL behaviour using ping tests and ACL hit counters

---

## Topology Reference

The screenshot below shows the V1 EVE-NG topology.

![V1 EVE-NG Topology](../../topology/v1/v1-eve-ng-topology.png)

The ACLs are applied on the branch routers:

| Router | Branch | Local Network | ACL Purpose |
|---|---|---|---|
| BR1 | Engineering | 192.168.30.0/24 | Blocks Engineering traffic to Sales |
| BR2 | Sales | 192.168.40.0/24 | Blocks Sales traffic to Engineering |

---

## Security Policy

The required traffic policy is:

| Source | Destination | Result | Reason |
|---|---|---|---|
| Engineering | Sales | Deny | Departments should not communicate directly |
| Sales | Engineering | Deny | Departments should not communicate directly |
| Engineering | HQ Admin / Servers | Permit | Engineering still requires access to HQ resources |
| Sales | HQ Admin / Servers | Permit | Sales still requires access to HQ resources |
| HQ networks | Branch networks | Permit | HQ management and services remain reachable |

---

## Network Summary

| Network | Purpose |
|---|---|
| 192.168.10.0/24 | HQ Admin VLAN |
| 192.168.20.0/24 | HQ Server VLAN |
| 192.168.30.0/24 | Engineering branch LAN |
| 192.168.40.0/24 | Sales branch LAN |

The ACLs only block traffic between the Engineering and Sales networks. All other required internal traffic remains permitted.

---

## ACL Design Approach

Extended ACLs were used because they can match both source and destination IP addresses.

This allows the lab to block only specific traffic flows instead of blocking an entire source network from reaching the rest of the enterprise.

The ACLs are placed inbound on the branch LAN interfaces.

This follows the common best-practice approach for extended ACLs:

```text
Place extended ACLs as close to the source as possible.
```

Applying the ACL inbound on the branch LAN interface means unwanted traffic is dropped before it crosses the WAN.

---

## ACL Placement

| Router | Interface | Direction | Reason |
|---|---|---|---|
| BR1 | Gi0/0 | Inbound | Filters Engineering traffic as it enters BR1 |
| BR2 | Gi0/0 | Inbound | Filters Sales traffic as it enters BR2 |

In this topology, `Gi0/0` is the branch LAN-facing interface on both branch routers.

| Router | LAN Interface | LAN IP |
|---|---|---|
| BR1 | Gi0/0 | 192.168.30.1/24 |
| BR2 | Gi0/0 | 192.168.40.1/24 |

---

## BR1 ACL - Block Engineering to Sales

BR1 is the Engineering branch router.

The ACL below blocks traffic from the Engineering subnet to the Sales subnet.

```cisco
ip access-list extended BLOCK_ENG_TO_SALES
 deny ip 192.168.30.0 0.0.0.255 192.168.40.0 0.0.0.255
 permit ip any any
```

The ACL is applied inbound on the Engineering LAN interface:

```cisco
interface GigabitEthernet0/0
 ip access-group BLOCK_ENG_TO_SALES in
```

This prevents Engineering users from sending traffic directly to the Sales branch network.

---

## BR2 ACL - Block Sales to Engineering

BR2 is the Sales branch router.

The ACL below blocks traffic from the Sales subnet to the Engineering subnet.

```cisco
ip access-list extended BLOCK_SALES_TO_ENG
 deny ip 192.168.40.0 0.0.0.255 192.168.30.0 0.0.0.255
 permit ip any any
```

The ACL is applied inbound on the Sales LAN interface:

```cisco
interface GigabitEthernet0/0
 ip access-group BLOCK_SALES_TO_ENG in
```

This prevents Sales users from sending traffic directly to the Engineering branch network.

---

## Why `permit ip any any` Is Required

Cisco ACLs include an implicit deny at the end.

This means that if only the deny statement was configured, all other traffic would also be blocked.

The explicit permit statement allows all other traffic after the blocked inter-branch traffic has been denied.

```cisco
permit ip any any
```

This ensures that:

- Engineering cannot reach Sales directly
- Sales cannot reach Engineering directly
- Engineering can still reach HQ resources
- Sales can still reach HQ resources
- General routed connectivity remains functional

---

## Verification Commands

The following commands were used to verify the ACL configuration.

| Command | Purpose |
|---|---|
| `show access-lists` | Confirms ACL entries and hit counters |
| `show ip interface gi0/0` | Confirms the ACL is applied inbound on the LAN interface |
| `show running-config section access-list` | Reviews ACL configuration |
| `show running-config interface gi0/0` | Reviews interface ACL placement |

---

## Verification Evidence

### Access List Verification

`show access-lists` was used to confirm that the ACLs were configured and that hit counters increased when matching traffic was tested.

<img width="885" height="358" alt="show access-lists output" src="https://github.com/user-attachments/assets/4bfd9ccf-2fce-4074-992f-8df9634c2ff2" />

---

### Interface ACL Verification

`show ip interface gi0/0` was used to confirm that the ACL was applied inbound on the branch LAN interface.

<img width="888" height="237" alt="show ip interface gi0/0 output" src="https://github.com/user-attachments/assets/3ce49e08-b2ee-440a-8aa5-efe53e12172b" />

---

## Connectivity Testing

Connectivity testing was performed to confirm that the ACLs matched the intended security policy.

---

## Expected to Fail

The following tests should fail because direct Engineering-to-Sales and Sales-to-Engineering traffic is blocked.

### Engineering to Sales

From the Engineering PC:

```cisco
ping 192.168.40.10
```

Expected result:

```text
Fail
```

<img width="875" height="325" alt="Engineering to Sales blocked ping test" src="https://github.com/user-attachments/assets/1bd0f577-e52f-439f-8b53-46550be7a5a0" />

---

### Sales to Engineering

From the Sales PC:

```cisco
ping 192.168.30.10
```

Expected result:

```text
Fail
```

<img width="882" height="335" alt="Sales to Engineering blocked ping test" src="https://github.com/user-attachments/assets/16a822fa-12d3-4eee-b9d0-b6d79f0c3d8a" />

---

## Expected to Succeed

The following tests should succeed because both branch departments are still allowed to access HQ resources.

### Engineering to HQ

From the Engineering PC:

```cisco
ping 192.168.10.10
ping 192.168.20.10
```

Expected result:

```text
Success
```

<img width="884" height="394" alt="Engineering to HQ successful ping test" src="https://github.com/user-attachments/assets/9ff6b704-f5e8-489f-8c99-6356f68c44ab" />

---

### Sales to HQ

From the Sales PC:

```cisco
ping 192.168.10.10
ping 192.168.20.10
```

Expected result:

```text
Success
```

<img width="878" height="443" alt="Sales to HQ successful ping test" src="https://github.com/user-attachments/assets/2a6f11dc-a889-417a-9571-d53c750c8413" />

---

## Test Summary

| Test | Expected Result | Outcome |
|---|---|---|
| Engineering to Sales | Fail | Passed |
| Sales to Engineering | Fail | Passed |
| Engineering to HQ Admin | Success | Passed |
| Engineering to HQ Servers | Success | Passed |
| Sales to HQ Admin | Success | Passed |
| Sales to HQ Servers | Success | Passed |

The results confirm that the ACLs block only the intended inter-branch traffic while allowing required access to HQ resources.

---

## Troubleshooting Notes

### ACL Direction

The ACLs were applied inbound on the branch LAN interfaces.

If the ACLs were applied in the wrong direction, the policy may not match traffic as intended.

For this lab:

- Engineering traffic enters BR1 on `Gi0/0`
- Sales traffic enters BR2 on `Gi0/0`

Therefore, inbound filtering on `Gi0/0` is the correct placement.

---

### ACL Order

ACL entries are processed from top to bottom.

The deny statement must appear before the permit statement.

Correct order:

```cisco
deny ip 192.168.30.0 0.0.0.255 192.168.40.0 0.0.0.255
permit ip any any
```

If `permit ip any any` was placed first, the deny statement would never be matched.

---

### Implicit Deny

Every ACL has an implicit deny at the end.

If the final `permit ip any any` statement was removed, traffic not matching the deny statement would still be dropped.

This would break required connectivity to HQ resources.

---

## Expected Results

At the end of this phase:

- BR1 blocks Engineering-to-Sales traffic
- BR2 blocks Sales-to-Engineering traffic
- Engineering can still access HQ VLANs
- Sales can still access HQ VLANs
- ACLs are applied inbound on branch LAN interfaces
- ACL hit counters increase during blocked traffic tests
- OSPF routing remains operational
- Required enterprise connectivity is preserved

---

## Design Notes

### Why Extended ACLs Were Used

Extended ACLs were used because the policy needed to match both source and destination networks.

A standard ACL would only match the source address, which would not be precise enough for this requirement.

### Why ACLs Were Applied Close to the Source

Applying the ACLs close to the source prevents unwanted traffic from crossing the WAN.

This is more efficient and follows common extended ACL placement guidance.

### Why ACLs Were Applied on Branch Routers

The branch routers are the Layer 3 boundary for the Engineering and Sales LANs.

Because the branch switches operate at Layer 2, the routers are the correct place to enforce Layer 3 traffic filtering.

### Why HQ Access Was Still Allowed

The goal was not to isolate the branches completely.

The goal was to block direct communication between Engineering and Sales while still allowing both departments to access shared HQ resources.

---

## Platform Note

This lab was built using virtual Cisco images in EVE-NG.

Some default or platform-generated CLI output may vary between devices. The published configurations and documentation focus on the relevant working configuration and design intent.

---

## Outcome

Extended ACLs were successfully implemented on the branch routers.

The ACLs enforced the required segmentation policy by blocking direct traffic between Engineering and Sales while preserving access to HQ networks.

At this stage, the V1 network includes both dynamic routing and basic Layer 3 security policy enforcement.

---

## Key Learning

This phase reinforced several important ACL concepts:

- Extended ACLs can match both source and destination IP addresses
- Extended ACLs should usually be placed close to the source
- ACL direction matters
- ACL rule order matters
- Cisco ACLs include an implicit deny at the end
- Hit counters are useful for confirming that traffic matches the intended ACL entries
- ACLs can enforce simple departmental segmentation without breaking required business access

---

## Related Documentation

- [Previous: 04 - Branch Switches](04-branch-switches.md)
- [Next: 06 - Testing and Validation](06-testing.md)
- [V1 Overview](./)
- [V1 Topology](../../topology/v1/)
- [Device Configurations](../../configs/)

---
