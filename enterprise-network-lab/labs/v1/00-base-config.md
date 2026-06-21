# 00 - Base Configuration

## Overview

This phase establishes a consistent baseline configuration across all routers and switches in the V1 Enterprise Network Lab.

The purpose of the base configuration is to prepare each device for secure management, consistent identification, and reliable operation before adding routing, switching, redundancy, and security features later in the lab.

This phase applies to:

- HQ core switches
- HQ access switches
- Branch switches
- HQ routers
- Branch routers
- Simulated ISP/WAN router

---

## Objective

The objective of this phase is to configure a standard management baseline across all network devices.

This includes:

- Device hostnames
- Local administrator accounts
- SSH management access
- Console access settings
- MOTD security banner
- Password encryption
- DNS lookup prevention
- Lab-specific EVE-NG interface recovery automation on routers

---

## Topology Reference

The screenshot below shows the V1 topology within EVE-NG.

Base configuration has been applied to every router and switch before progressing to VLANs, routing, redundancy, and security configuration.

![V1 EVE-NG Topology](../../topology/v1/v1-eve-ng-topology.png)

---

## Devices Configured

| Device Type | Devices |
|---|---|
| HQ Core Switches | HQ-CSW1, HQ-CSW2 |
| HQ Access Switches | HQ-ASW1, HQ-ASW2, HQ-SSW1, HQ-SSW2 |
| Branch Switches | BR-SW1, BR-SW2 |
| HQ Routers | HQ-R1, HQ-R2 |
| Branch Routers | BR1, BR2 |
| WAN / Provider Router | ISP |

---

## Key Features Implemented

The base configuration includes:

- Hostname configuration for device identification
- DNS lookup disabled to prevent delays from mistyped commands
- Local privilege 15 administrator account
- Password encryption for locally stored passwords
- SSH version 2 enabled for remote management
- RSA key generation for encrypted SSH access
- Console login using the local user database
- VTY lines restricted to SSH access only
- MOTD banner for basic access warning
- EEM applet on routers to restore interface state after lab restarts

---

## Example Base Configuration

The example below shows the standard base configuration approach used across the lab devices.

```cisco
hostname HQ-CSW1
service password-encryption
no ip domain-lookup

banner motd ^
Unauthorized access is prohibited
^

username admin privilege 15 secret <your secret>

ip domain-name lab.local
crypto key generate rsa
ip ssh version 2

line console 0
 logging synchronous
 login local

line vty 0 15
 login local
 transport input ssh
```

> Note: Lab credentials are represented using placeholders in the published configuration files. Real production environments should use strong unique credentials, role-based access control, centralised authentication where appropriate, and secure credential storage.

---

## SSH Management Design

SSH is used instead of Telnet to provide encrypted remote management access.

The SSH configuration requires:

- A domain name
- RSA keys
- SSH version 2
- A local user account
- VTY lines configured for SSH-only access

This allows devices to be managed securely from elsewhere in the lab once IP addressing and routing are configured.

---

## EVE-NG Interface Automation

During testing, some EVE-NG router images did not consistently preserve interface state after device reloads.

To work around this lab behaviour, an Embedded Event Manager applet was added to routers. This automatically brings the required router interfaces back up after a restart.

Example:

```cisco
event manager applet AUTO-UP-INTERFACES
 event syslog pattern "SYS-5-RESTART"
 action 1.0 cli command "enable"
 action 2.0 cli command "conf t"
 action 3.0 cli command "interface range g0/0-2"
 action 4.0 cli command "no shutdown"
 action 5.0 cli command "end"
```

The interface range varies depending on the router.

For example:

| Device | Interface Range Used |
|---|---|
| HQ-R1 | `g0/0-2` |
| HQ-R2 | `g0/0-2` |
| BR1 | `g0/0-1` |
| BR2 | `g0/0-1` |
| ISP | `g0/0-3` |

This EEM applet is used only as a lab workaround for router interface behaviour in EVE-NG. Switches did not require this workaround.

---

## Verification Commands

The following commands were used to verify the base configuration.

| Command | Purpose |
|---|---|
| `show running-config` | Confirms the configured baseline settings |
| `show ip ssh` | Verifies that SSH is enabled |
| `show users` | Shows active management sessions |
| `show line` | Checks console and VTY line configuration |

---

## Verification Evidence

### Running Configuration Check

`show running-config` was used to confirm that the baseline configuration was applied.

<img width="882" height="667" alt="show running-config output" src="https://github.com/user-attachments/assets/a477753b-9161-4c11-a609-1caf4f18b316" />

---

### SSH Verification

`show ip ssh` confirmed that SSH was enabled on the device.

<img width="868" height="318" alt="show ip ssh output" src="https://github.com/user-attachments/assets/613afd73-d0bc-45b3-81af-ea5053b80d7d" />

---

### User Session Verification

`show users` was used to confirm active management access.

<img width="878" height="146" alt="show users output" src="https://github.com/user-attachments/assets/d08f4341-c164-4641-8d9b-27926ef52d40" />

---

### Line Configuration Verification

`show line` was used to check console and VTY line status.

<img width="880" height="427" alt="show line output" src="https://github.com/user-attachments/assets/d6b10d78-5a18-4941-b83f-7b9b5ed89ab7" />

---

## Outcome

At the end of this phase:

- All devices had clear hostnames
- Local administrative access was configured
- SSH management was enabled
- Telnet access was not used
- Console access was standardised
- Basic security banner and password encryption were applied
- Router interfaces were made more reliable after EVE-NG reloads using EEM
- The lab was ready for VLAN, switching, routing, and redundancy configuration

---

## Key Learning

This phase reinforced the importance of building a consistent device baseline before configuring more advanced features.

Key learning points:

- A standard base configuration makes multi-device labs easier to manage
- SSH should be used instead of Telnet for encrypted management access
- Local authentication provides a simple management method for lab environments
- Consistent hostnames and banners improve clarity during troubleshooting
- Platform-specific lab issues, such as EVE-NG interface behaviour, sometimes require practical workarounds
- EEM can be used to automate repetitive recovery tasks and improve lab reliability

---

## Related Documentation

- [V1 Overview](./)
- [Next: 01 - Core Setup](01-core-setup.md)
- [V1 Topology](../../topology/v1/)
- [Device Configurations](../../configs/)

---
