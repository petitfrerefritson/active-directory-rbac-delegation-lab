# Active Directory RBAC & Delegation Lab

## Overview

This project demonstrates the implementation of enterprise Role-Based Access Control (RBAC) within Active Directory.


## Business Problem

In many organizations, permissions are assigned directly to users, creating administrative overhead, inconsistent access controls, and increased risk of permission creep.

This project demonstrates how Role-Based Access Control (RBAC) can be implemented in Active Directory using Microsoft's AGDLP methodology to provide scalable, auditable, and least-privilege access management.

The lab specifically focuses on controlling Remote Desktop access to standard and privileged workstations while maintaining clear separation between identity, policy, and permissions

<br>
The objective was to create a scalable and secure permission model that separates identity, policy, and authorization while following least privilege principles.

<br>
<br>

![Windows Server](https://img.shields.io/badge/Windows%20Server-2025-blue)
![Active Directory](https://img.shields.io/badge/Active%20Directory-RBAC-red)
![Group Policy](https://img.shields.io/badge/Group%20Policy-GPO-purple)
![IAM](https://img.shields.io/badge/IAM-AGDLP-green)

<br>
<br>

<br>

<p align="center">
<img src="screenshots/Active Directory RBAC architecture overview.png" align="center">
</p>

<br>

## Lab Environment

| Component | Technology |
|------------|------------|
| Hypervisor | Proxmox VE |
| Domain Controller | Windows Server 2025 |
| Domain | darkhorse.internal |
| Management Workstation | MGMT01 |
| Standard Workstation | WS-TEST01 |
| Directory Service | Active Directory Domain Services (AD DS) |
| Policy Management | Group Policy |
| Access Model | AGDLP |
| Authorization Method | Security Groups |

<br>
<br>

### Validation Results

<br>

| Test | Expected Result | Outcome|
|------|-----------------|--------|
| Helpdesk → WS-TEST01 | RDP Allowed | PASS |
| Helpdesk → MGMT01 | RDP Denied | PASS |
| Infra Admin → WS-TEST01 | RDP Allowed | PASS |
| Infra Admin → MGMT01 | RDP Allowed | PASS |
| Remove Group Membership | Access Revoked | PASS |

<br>

## Project Documentation

- [RBAC Fundamentals](docs/01-rbac-fundamentals.md)
- [OU Design](docs/02-ou-design.md)
- [RDP Authorization Implementation](docs/03-rdp-rbac-implementation.md)
- [Validation Testing](docs/04-validation.md)
- [Troubleshooting](docs/05-troubleshooting.md)
- [Lessons Learned](docs/06-lessons-learned.md)

<br>

### Technologies Used

- Windows Server 2025
- Active Directory Domain Services
- Group Policy
- Organizational Units
- Security Groups
- Remote Desktop Services
- Windows Firewall
- AGDLP

<br>
<br>

### Architecture

<br>

### Organizational Units

```text
Workstations
├── Standard
│   └── WS-TEST01
│
└── Admin Workstations
    └── MGMT01



Global Security Groups:

GG_HELPDESK
GG_INFRA_ADMIN



Domain Local Groups:

DL_WORKSTATION_RDP
DL_ADMIN_WORKSTATION_RDP


AGDLP Flow

User
↓
Global Group
↓
Domain Local Group
↓
Permission
↓
Resource
```

<br>

### Key Concepts

<br>

- RBAC
- AGDLP
- Least Privilege
- Identity vs Permissions
- OU Design
- Delegated Administration

<br>

### Project Documentation

<br>


- RBAC Fundamentals
- OU Design
- RDP Authorization Implementation
- Validation Testing
- Troubleshooting
- Lessons Learned

<br>

### Skills Demonstrated
<br>

- Active Directory Administration
- Group Policy Management
- RBAC Design
- Access Control
- Windows Administration
- Troubleshooting

<br>

### Key Lessons Learned

<br>

- OUs organize policy, not permissions.
- Security Groups determine authorization.
- AGDLP creates scalable permission chains.
- Group Policy configuration and authorization should be separated.
- OU inheritance directly impacts GPO application.
- "Update" and "Replace" behave differently within Group Policy Preferences.
- Least privilege is easier to enforce when permissions are assigned through Domain Local groups.

<br>

#### Resume Bullet

Designed and implemented enterprise Active Directory Role-Based Access Control (RBAC) using AGDLP, Organizational Units, Group Policy, delegated administration, and least-privilege principles to control Remote Desktop authorization across standard and privileged workstation tiers.

<br>