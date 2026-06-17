
# Problem

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

<br>

### Accounts

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

<br>

## Implementation

Implementing Role-Based Access Control (RBAC) requires separating system configuration from authorization.

In this implementation, Remote Desktop access was managed using Microsoft's AGDLP methodology combined with Group Policy.

Rather than assigning Remote Desktop permissions directly to users, permissions were assigned to Domain Local Security Groups. User accounts were then granted access through Global Security 

Groups representing organizational roles.

To accomplish this, three Group Policies were created:

GPO-RDP-Base

This policy was linked to the parent Workstations OU and was responsible for configuring the 
operating system to support Remote Desktop.

Configuration included:

- Enable Remote Desktop
- Allow Remote Desktop through Windows Firewall
- Configure required RDP settings

This policy performs configuration only and does not grant authorization.

GPO-Workstation-RDP

This policy was linked to the Standard Workstations OU.

The policy grants the DL_Workstation_RDP Domain Local Group permission to connect through Remote Desktop Services.

GPO-Admin-Workstation-RDP

This policy was linked to the Admin Workstations OU.

The policy grants the DL_Admin_Workstation_RDP Domain Local Group permission to connect through Remote Desktop Services.

## Authorization Flow

User accounts are assigned to Global Security Groups based on job function.

Global Groups are then nested into Domain Local Groups which own permissions.

```text
User Account
    ↓
Global Group
    ↓
Domain Local Group
    ↓
Resource Permission
Helpdesk Access Path
Fritson
    ↓
GG_HELPDESK
    ↓
DL_Workstation_RDP
    ↓
Remote Desktop Access
    ↓
WS-TEST01
Infrastructure Administrator Access Path
Infrastructure Administrator
    ↓
GG_INFRA_ADMIN
    ↓
DL_Admin_Workstation_RDP
    ↓
Remote Desktop Access
    ↓
MGMT01
```

### Result

This design ensures that permissions are never assigned directly to user accounts.
When a new employee joins the Helpdesk team, administrators only need to add the user to the GG_HELPDESK Global Group.

Access is automatically inherited through nested group membership, reducing administrative overhead, simplifying auditing, and preventing permission creep.

This approach scales significantly better than assigning permissions directly to users and mirrors common enterprise Active Directory access management practices.

## Project Documentation

- [RBAC Fundamentals](docs/01-rbac-fundamentals.md)
- [OU Design](docs/02-ou-design.md)
- [Validation Testing](docs/04-validation.md)
- [Troubleshooting](docs/05-troubleshooting.md)
- [Lessons Learned](docs/06-lessons-learned.md)