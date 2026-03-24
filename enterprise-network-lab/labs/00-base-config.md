# Base Configuration

## Objective

Establish a secure and consistent baseline configuration across all network devices, including routers and switches.

---

## Key Features Implemented

* Hostname configuration for device identification
* Disabled DNS lookup to prevent delays from mistyped commands
* Secure enable password using `enable secret`
* Local user account creation for authentication
* Console access configuration with logging synchronisation
* SSH remote access (version 2) enabled
* RSA key generation for encrypted management access
* VTY lines configured to allow SSH only
* Password encryption enabled
* MOTD banner configured for security awareness

---

## Devices Configured

* Core Switches: CSW1, CSW2
* Access Switches: HQ-ASW1, HQ-ASW2, HQ-SSW1, HQ-SSW2
* Routers: HQ-R1, HQ-R2, BR1, BR2, ISP

---

## Example Configuration (CSW1)

```bash
hostname CSW1
no ip domain-lookup

enable secret cisco123
service password-encryption

username admin privilege 15 secret admin123

ip domain-name lab.local

crypto key generate rsa
ip ssh version 2

line console 0
 logging synchronous
 exec-timeout 10 0
 login local

line vty 0 15
 transport input ssh
 login local

banner motd #
Unauthorized access is prohibited
#
```

---

## Verification Commands

* `show running-config`
* `show ip ssh`
* `show users`
* `show line`

---

## Key Learning

* Establishing a consistent baseline configuration simplifies management across multiple devices
* SSH provides secure remote access compared to Telnet
* Local authentication ensures controlled access to devices
* Proper console configuration improves usability during troubleshooting
