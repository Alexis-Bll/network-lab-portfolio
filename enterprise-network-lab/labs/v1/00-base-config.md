# Base Configuration

## Objective

Establish a secure and consistent baseline configuration across all network devices, including routers and switches.

---

## Topology Overview
The screenshot shows the network topology within Eve-ng. Base configurations have been applied to every network device within the toplogy.

![HQ Topology](../topology/v1-eve-ng-topology.png)

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

* Core Switches: HQ-CSW1, HQ-CSW2
* Access Switches: HQ-ASW1, HQ-ASW2, HQ-SSW1, HQ-SSW2,
* Routers: HQ-R1, HQ-R2, BR1, BR2, ISP
* Branch Switches: BR-SW1, BR-SW2

---

## Example Configuration (HQ-CSW1)

```cisco
hostname HQ-CSW1
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

## Interface Automation (EVE-NG Workaround)

Due to certain EVE-NG router images not preserving interface states after reboot, an Embedded Event Manager (EEM) applet is used to automatically bring interfaces up on boot.

This ensures consistent device behaviour across lab restarts without requiring manual intervention.

```cisco
event manager applet AUTO-UP-INTERFACES
 event none
 action 1.0 cli command "enable"
 action 2.0 cli command "conf t"
 action 3.0 cli command "interface range g0/0 - 2"
 action 4.0 cli command "no shutdown"
```

The range may vary per router depending on what interfaces need to be up and operational.

This configuration is applied to routers only (BR1, BR2, ISP, HQ-R1, HQ-R2), as switches retain interface state correctly within the lab environment.

---

## Verification Commands

* `show running-config`
<img width="882" height="667" alt="image" src="https://github.com/user-attachments/assets/a477753b-9161-4c11-a609-1caf4f18b316" />

* `show ip ssh`
<img width="868" height="318" alt="image" src="https://github.com/user-attachments/assets/613afd73-d0bc-45b3-81af-ea5053b80d7d" />

* `show users`
<img width="878" height="146" alt="image" src="https://github.com/user-attachments/assets/d08f4341-c164-4641-8d9b-27926ef52d40" />

* `show line`
<img width="880" height="427" alt="image" src="https://github.com/user-attachments/assets/d6b10d78-5a18-4941-b83f-7b9b5ed89ab7" />

---

## Key Learning

* Establishing a consistent baseline configuration simplifies management across multiple devices
* SSH provides secure remote access compared to Telnet
* Local authentication ensures controlled and secure access to network devices
* Proper console configuration improves usability during troubleshooting
* Identifying and working around platform limitations (EVE-NG interface state issue) reinforces the importance of adaptability in lab environments
* Using automation (EEM applets) to maintain consistent device behaviour demonstrates practical problem-solving beyond standard configuration tasks

--- 
