## Problem

Assigning permissions directly to user accounts creates administrative overhead, increases the likelihood of permission creep, and makes auditing more difficult as environments grow.

As users change roles, join new teams, or leave the organization, administrators must manually modify permissions across multiple systems and resources.

To address these challenges, Microsoft recommends the AGDLP methodology, which separates identity from authorization and creates a scalable permission management model.

<br>

```text
Accounts
↓
Global Groups
↓
Domain Local Groups
↓
Permissions
↓
Resources
```


This approach simplifies access management, improves auditing, and reduces operational complexity by managing permissions through security groups rather than individual user accounts.

<br>

## Requirements

The solution needed to:

- Allow Helpdesk staff to access standard workstations
- Prevent Helpdesk staff from accessing administrative workstations
- Allow Infrastructure Administrators to access both workstation tiers
- Avoid assigning permissions directly to user accounts
- Support future growth without redesigning permissions
- Follow Microsoft's AGDLP model 

<br>

## Design

#### Accounts

User accounts represent individual identities within Active Directory.

Users are assigned to Global Security Groups based on their job function or administrative responsibilities.

Example:
- Fritson → GG_HELPDESK
- Infrastructure Administrator → GG_INFRA_ADMIN


#### Global Groups

Global Security Groups represent business roles within the organization.

Examples include:
- GG_HELPDESK
- GG_INFRA_ADMIN
- GG_SERVER_ADMIN
- GG_SECURITY_ADMIN

These groups contain users who share the same responsibilities and access requirements.


#### Domain Local Groups

Domain Local Security Groups own permissions to resources.

Rather than assigning permissions directly to users or Global Groups, permissions are assigned to Domain Local Groups.

Examples include:
- DL_Workstation_RDP
- DL_Admin_Workstation_RDP
- DL_Server_RDP
- DL_Server_Local_Admins

Global Groups are nested into Domain Local Groups to grant access.

Example:

```text
GG_HELPDESK
↓
DL_Workstation_RDP
↓
Remote Desktop Access to Standard Workstations
```

This design separates identity from authorization, improving scalability and maintainability.
