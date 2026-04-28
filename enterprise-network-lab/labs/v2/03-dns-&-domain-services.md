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

<img width="794" height="665" alt="image" src="https://github.com/user-attachments/assets/3cfad976-9303-4a15-92f4-58c661094f07" />

---
















<img width="806" height="661" alt="image" src="https://github.com/user-attachments/assets/e1eef2db-21b0-4788-9566-b04bb6451558" />

---

<img width="801" height="664" alt="image" src="https://github.com/user-attachments/assets/1fa464c8-3333-4234-b994-8526f0bfdd72" />

---

<img width="796" height="659" alt="image" src="https://github.com/user-attachments/assets/b64c2d1f-3d8a-4a6f-9218-d544506320d6" />

---

<img width="791" height="654" alt="image" src="https://github.com/user-attachments/assets/8df513b0-e5a5-457a-8810-b47880a0fb0d" />








