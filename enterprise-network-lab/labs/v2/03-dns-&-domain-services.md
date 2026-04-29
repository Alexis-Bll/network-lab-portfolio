# 03 – DNS & Domain Services

## Overview

This section documents the deployment of core identity and name resolution services within the enterprise lab environment.

A Windows Server domain controller (DC1) was configured to provide:

- Active Directory Domain Services (AD DS)
- DNS (Active Directory-integrated)

This enables centralized authentication, authorization, and internal name resolution across multiple VLANs in the network.

---

## Objectives

- Deploy an Active Directory domain (`lab.local`)
- Configure DNS for internal name resolution
- Enable domain joins across multiple VLANs
- Implement structured OU design
- Configure group-based access control
- Validate authentication and resource access

---

## 1. Active Directory Domain Deployment

Active Directory Domain Services were installed on **DC1**, and a new domain was created:

```
lab.local
```

DNS was integrated with Active Directory, allowing:

- Secure dynamic updates
- Centralized name resolution
- Scalability for future domain controllers

<img width="797" height="659" alt="image" src="https://github.com/user-attachments/assets/58741d94-1a39-45d6-8215-78f3486661eb" />

---

## 2. DNS Configuration & Validation

DNS was configured as an **AD-integrated zone**, providing:

- Secure dynamic DNS updates
- Automatic client record registration
- Replication support for future DC expansion

Clients registered DNS records using:

```bash
ipconfig /registerdns
```

### Validation

- Forward lookups successfully resolved hostnames
- Clients dynamically populated DNS records

<img width="793" height="660" alt="image" src="https://github.com/user-attachments/assets/d5900bfc-9b26-412b-a20c-83cc522b2d53" />

---

#### DNS Resolution Test

<img width="794" height="665" alt="image" src="https://github.com/user-attachments/assets/3cfad976-9303-4a15-92f4-58ec1aa8bf05" />

---

### Troubleshooting Note

During testing, DNS registration and resolution were manually validated using:

```bash
ipconfig /registerdns
nslookup <hostname>
```

This ensured that:

- Clients were correctly registering with DNS
- Name resolution was functioning across VLANs
- The domain controller was being used as the primary DNS server

This step helped identify and confirm proper DNS behaviour before proceeding with domain joins.

---

## 3. Domain Join Across VLANs

Client machines in multiple VLANs (Admin, Engineering, Sales) were successfully joined to the domain.

Each client received:

- IP address via DHCP
- DNS configuration pointing to DC1
- Authentication services via Active Directory

This confirms proper **inter-VLAN routing + DNS + AD integration**.

<img width="795" height="661" alt="image" src="https://github.com/user-attachments/assets/876e85c7-510e-430e-a1a9-b1033cede204" />

---

<img width="797" height="657" alt="image" src="https://github.com/user-attachments/assets/08ab3f22-a07c-4232-9c6b-5ef11178191c" />

---

## 4. Organizational Unit (OU) Design

A structured OU hierarchy was implemented to reflect departmental separation:

```
lab.local
├── Admin
├── Engineering
├── Sales
├── Servers
├── Workstations
```

This design supports:

- Logical organization of users and devices
- Targeted Group Policy application
- Scalable administration

<img width="794" height="661" alt="image" src="https://github.com/user-attachments/assets/6751e1c3-5f33-453c-b46b-da11b476d9cb" />

---

## 5. User & Group Management

Users were created and assigned based on department:

**Users**
- alex.admin
- eng.user1
- sales.user1

**Security Groups**
- ENG-Users
- SALES-Users

Users were added to their respective groups to enable **role-based access control (RBAC)**.

<img width="799" height="666" alt="image" src="https://github.com/user-attachments/assets/ba07a310-3e75-46b5-8ef1-5e91716fdf09" />

---

<img width="798" height="659" alt="image" src="https://github.com/user-attachments/assets/6de9f2d5-701f-45d0-930d-93477c572c7b" />

---

## 6. Authentication Verification

Domain authentication was validated using:

```
LAB\username
```

Commands used:

```bash
whoami
whoami /groups
```

This confirmed:

- Successful domain authentication
- Correct group membership assignment

<img width="795" height="659" alt="image" src="https://github.com/user-attachments/assets/35ca7340-9cbf-42d1-81a5-c33b307d2f2c" />

---

## 7. Shared Folder & Access Control

A shared directory was created on DC1:

```
C:\Shares\Shared-Internal
```

### Permissions Configuration

**NTFS Permissions**
- Domain Admins → Full Control
- ENG-Users → Modify
- SALES-Users → No Access

**Share Permissions**
- Restricted to Admin and Engineering groups

This demonstrates layered permission control using:

- NTFS permissions (security)
- Share permissions (network access)

<img width="800" height="666" alt="image" src="https://github.com/user-attachments/assets/4809bc25-8393-4c5d-a06e-96cfccc888f1" />

---

## 8. Access Validation

Access to the shared resource was tested across departments:

| User Type   | Access Result |
|------------|--------------|
| Admin       | Allowed      |
| Engineering | Allowed      |
| Sales       | Denied       |

This confirms correct enforcement of **group-based access policies**.

<img width="796" height="659" alt="image" src="https://github.com/user-attachments/assets/b64c2d1f-3d8a-4a6f-9218-d544506320d6" />

---

<img width="796" height="658" alt="image" src="https://github.com/user-attachments/assets/b8384f14-6c73-4d02-9a1f-58ec1aa8bf05" />

---

<img width="791" height="654" alt="image" src="https://github.com/user-attachments/assets/8df513b0-e5a5-457a-8810-b47880a0fb0d" />

---

## 9. Group Policy – Access Restrictions

### Overview

Group Policy was used to enforce role-based restrictions on system settings.

### Objective

- Sales users → No Control Panel access
- Engineering users → Full access
- Admin users → Full access

---

### Configuration

A GPO was created and linked to the **Sales OU**.

Policy path:

```
User Configuration → Administrative Templates → Control Panel
```

Setting applied:

```
Prohibit access to Control Panel and PC settings = Enabled
```

---

### Deployment

Policy was applied using:

```bash
gpupdate /force
```

Users logged off and back on for changes to take effect.

---

### Validation

- Sales users → Blocked from Control Panel
- Engineering users → Full access
- Admin users → Full access

---

### Outcome

This demonstrates effective use of **Group Policy for role-based access control**, ensuring:

- Restricted access for non-technical users
- Full control retained for administrators
- Consistent policy enforcement across the domain

<img width="806" height="661" alt="image" src="https://github.com/user-attachments/assets/e1eef2db-21b0-4788-9566-b04bb6451558" />

---

## Summary

This lab successfully demonstrates the deployment of enterprise identity services, including:

- Active Directory domain setup
- DNS integration and validation
- Multi-VLAN domain authentication
- Structured OU design
- Role-based access control (RBAC)
- Group Policy enforcement

These components form the foundation of a scalable enterprise network and align with real-world infrastructure design practices.
