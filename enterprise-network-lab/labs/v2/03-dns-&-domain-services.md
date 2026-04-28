# 03 – DNS & Domain Services

## Overview

This section covers the deployment of core identity and name resolution services within the enterprise lab. A Windows Server domain controller (DC1) was configured to provide Active Directory Domain Services (AD DS) and DNS, enabling centralized authentication, authorization, and resource access control across multiple VLANs.

---

## Objectives

- Deploy Active Directory domain (lab.local)
- Configure DNS for internal name resolution
- Join clients to the domain across VLANs
- Organize users using Organizational Units (OUs)
- Implement group-based access control
- Validate domain authentication and resource access

---

### 1. Active Directory Domain Setup

Active Directory Domain Services were installed on DC1 and a new domain was created:

```lab.local```

DNS was integrated with AD, allowing secure dynamic updates and centralized name resolution.

<img width="797" height="659" alt="image" src="https://github.com/user-attachments/assets/58741d94-1a39-45d6-8215-78f3486661eb" />

---

### 2. DNS Configuration & Validation

DNS was configured as an Active Directory-integrated zone, allowing:

- Secure dynamic updates
- Automatic client registration
- Replication across domain controllers (future scalability)

Clients successfully registered their records using:

bash``` ipconfig /registerdns```

---

<img width="793" height="660" alt="image" src="https://github.com/user-attachments/assets/d5900bfc-9b26-412b-a20c-83cc522b2d53" />

---

#### DNS is resolving hosts

<img width="794" height="665" alt="image" src="https://github.com/user-attachments/assets/3cfad976-9303-4a15-92f4-58c661094f07" />

---

## 3. Domain Join (Multi-VLAN Clients)

Client machines across multiple VLANs (Admin, Engineering, Sales) were joined to the domain.

Each client received:

- IP via DHCP
- DNS pointing to DC1
- Domain authentication via AD

---

<img width="795" height="661" alt="image" src="https://github.com/user-attachments/assets/876e85c7-510e-430e-a1a9-b1033cede204" />

---

<img width="797" height="657" alt="image" src="https://github.com/user-attachments/assets/08ab3f22-a07c-4232-9c6b-5ef11178191c" />

---

## 4. Organizational Unit (OU) Design

To structure the environment logically, OUs were created:

lab.local
├── Admin
├── Engineering
├── Sales
├── Servers
├── Workstations

---

<img width="794" height="661" alt="image" src="https://github.com/user-attachments/assets/6751e1c3-5f33-453c-b46b-da11b476d9cb" />

---

## 5. User & Group Management

Users were created per department:

- alex.admin
- eng.user1
- sales.user1

Security groups were created:

- ENG-Users
- SALES-Users

Users were assigned to their respective groups.

---

<img width="799" height="666" alt="image" src="https://github.com/user-attachments/assets/ba07a310-3e75-46b5-8ef1-5e91716fdf09" />

---

<img width="798" height="659" alt="image" src="https://github.com/user-attachments/assets/6de9f2d5-701f-45d0-930d-93477c572c7b" />

---

## 6. Authentication Verification

Users successfully logged into domain-joined machines using:

```LAB\username```

Validation performed using:

```whoami```
```whoami /groups```

---

<img width="795" height="659" alt="image" src="https://github.com/user-attachments/assets/35ca7340-9cbf-42d1-81a5-c33b307d2f2c" />

---

## 7. Shared Folder & Access Control

A shared folder was created on DC1:

```C:\Shares\Shared-Internal```

This was configured with:

NTFS Permissions:
- Domain Admins → Full Control
- ENG-Users → Modify
- SALES-Users → No access

Share Permissions:
- Restricted to Admin + Engineering groups

---

<img width="800" height="666" alt="image" src="https://github.com/user-attachments/assets/4809bc25-8393-4c5d-a06e-96cfccc888f1" />

---

## 8. Access Validation

Access to the shared folder was tested across departments:

| User Type   | Access Result |
| ----------- | ------------- |
| Admin       | ✅ Allowed     |
| Engineering | ✅ Allowed     |
| Sales       | ❌ Denied      |


---

## 10. Group Policy – Access Control

### Overview

Group Policy was used to restrict access to system settings based on department.

### Objective
- Sales users → ❌ No Control Panel access
- Engineering users → ✅ Full access
- Admin users → ✅ Full access

---

### Configuration

A Group Policy Object (GPO) was created and linked to the Sales OU.

Path:

```User Configuration → Administrative Templates → Control Panel```

Setting applied:

```Prohibit access to Control Panel and PC settings = Enabled```

---


### Deployment

The policy was applied using:

```gpupdate /force```

Users then logged off and back on for the policy to take effect.

---

### Validation

- Sales user → blocked with restriction error when opening Control Panel
- Engineering user → full access confirmed
- Admin user → full access confirmed

---

### Outcome

This demonstrates role-based access control using Group Policy, ensuring non-technical users cannot modify system settings while administrators retain full control.



<img width="796" height="659" alt="image" src="https://github.com/user-attachments/assets/b64c2d1f-3d8a-4a6f-9218-d544506320d6" />

---

<img width="796" height="658" alt="image" src="https://github.com/user-attachments/assets/b8384f14-6c73-4d02-9a1f-58ec1aa8bf05" />

---


<img width="791" height="654" alt="image" src="https://github.com/user-attachments/assets/8df513b0-e5a5-457a-8810-b47880a0fb0d" />

---






<img width="806" height="661" alt="image" src="https://github.com/user-attachments/assets/e1eef2db-21b0-4788-9566-b04bb6451558" />

---

<img width="801" height="664" alt="image" src="https://github.com/user-attachments/assets/1fa464c8-3333-4234-b994-8526f0bfdd72" />










