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














<img width="883" height="671" alt="image" src="https://github.com/user-attachments/assets/214fdbca-67a3-414d-a002-d89cde7208a7" />

---

<img width="881" height="671" alt="image" src="https://github.com/user-attachments/assets/034c6641-5f0c-4b25-9249-e2ae70072255" />

---

<img width="880" height="670" alt="image" src="https://github.com/user-attachments/assets/2b08bb71-7670-4623-9bc4-dd819012350b" />

---

<img width="882" height="670" alt="image" src="https://github.com/user-attachments/assets/55849616-3000-4a3d-87ab-7be3a4970102" />

---

<img width="889" height="672" alt="image" src="https://github.com/user-attachments/assets/770f5803-f762-4cc0-acac-d84ceec5461f" />

---

<img width="797" height="337" alt="image" src="https://github.com/user-attachments/assets/02b15dea-61fa-4ba1-9675-ea1aad855d9d" />

---

<img width="893" height="324" alt="image" src="https://github.com/user-attachments/assets/1e7a4b9b-fb3b-4f71-98d9-c3a5465efbac" />

---

<img width="888" height="665" alt="image" src="https://github.com/user-attachments/assets/2955f4f1-b1e2-46b2-9cb7-37d4b6ad6cf1" />

---

<img width="889" height="668" alt="image" src="https://github.com/user-attachments/assets/6fe1d2e8-73f8-4975-8f77-3a289a23ee7e" />

---

<img width="891" height="672" alt="image" src="https://github.com/user-attachments/assets/f00c1bbe-9106-418e-bb28-594e093eb8e4" />

---

<img width="795" height="660" alt="image" src="https://github.com/user-attachments/assets/f61cd165-91cd-4832-82ef-9dc7f34fbee3" />

---

<img width="806" height="661" alt="image" src="https://github.com/user-attachments/assets/e1eef2db-21b0-4788-9566-b04bb6451558" />

---

<img width="801" height="664" alt="image" src="https://github.com/user-attachments/assets/1fa464c8-3333-4234-b994-8526f0bfdd72" />

---

<img width="796" height="659" alt="image" src="https://github.com/user-attachments/assets/b64c2d1f-3d8a-4a6f-9218-d544506320d6" />

---

<img width="791" height="654" alt="image" src="https://github.com/user-attachments/assets/8df513b0-e5a5-457a-8810-b47880a0fb0d" />








