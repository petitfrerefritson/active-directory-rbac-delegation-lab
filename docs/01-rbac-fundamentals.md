# Role-Based Access Control (RBAC) Fundamentals


## Overview

Role-Based Access Control (RBAC) is an access management model that grants permissions based on a user's job function rather than assigning permissions directly to individual user accounts.
In an enterprise environment, users are placed into security groups that represent their organizational role. These groups are then granted access to resources through additional permission groups, creating a scalable and manageable access control structure.
This approach follows the principle of least privilege by ensuring users receive only the permissions required to perform their job duties.

## Business Problem

Consider a Help Desk team responsible for providing remote support to employee workstations.

A poorly designed environment might grant Remote Desktop access directly to each Help Desk technician. As the organization grows, managing permissions at the individual user level becomes difficult, time-consuming, and prone to mistakes.

Problems with direct permission assignments include:
    • Increased administrative overhead
    • Difficult auditing and reporting
    • Higher risk of permission creep
    • Inconsistent access control
    • Greater likelihood of configuration errors

## RBAC Solution

Instead of assigning permissions directly to users, permissions are assigned to groups.

<h4>Example Design</h4>

Global Security Group
    • GG_Helpdesk

Domain Local Security Group
    • DL_Workstation_RDP

Configuration
    1. Add Help Desk users to GG_Helpdesk
    2. Add GG_Helpdesk as a member of DL_Workstation_RDP
    3. Grant Remote Desktop permissions to DL_Workstation_RDP

Access Flow

User → GG_Helpdesk → DL_Workstation_RDP → Remote Desktop Permission

This creates a clear chain of trust that is easy to understand and maintain.

Why Use a Domain Local Group?

Separating job roles from permissions provides several advantages:

Better Visibility

The purpose of each group becomes immediately obvious.

Examples:

| Group | Purpose |
|-------|---------|
| GG_Helpdesk |	Represents Help Desk personnel |
| DL_Workstation_RDP | Represents RDP access to workstations |
| DL_Reset_Passwords| Represents password reset permissions |
| DL_Local_Admin_Workstations | Represents local administrator rights |

#### Easier Auditing

Administrators can quickly determine:

- Who has access
- Why they have access
- Which business role requires that access

#### Simplified Management

When a new Help Desk technician joins:

- Add them to GG_Helpdesk
- They automatically inherit all Help Desk permissions

When a technician leaves:

- Remove them from GG_Helpdesk
- All inherited permissions are removed automatically

No individual resource permissions need to be modified.

#### AGDLP Model

This lab follows Microsoft's AGDLP methodology:

A → G → DL → P
- A = Accounts (Users)
- G = Global Groups (Job Roles)
- DL = Domain Local Groups (Resource Permissions)
- P = Permissions

Example

Fritson
    ↓
GG_Helpdesk
    ↓
DL_Workstation_RDP
    ↓
Remote Desktop Access

This model provides a scalable and enterprise-friendly approach to access management.
Validation
To verify the RBAC configuration:
    1. Create a test Help Desk user
    2. Add the user to GG_Helpdesk
    3. Confirm GG_Helpdesk is a member of DL_Workstation_RDP
    4. Log in as the test user
    5. Attempt to RDP to a workstation
    6. Verify access is successful
Then:
    1. Remove the user from GG_Helpdesk
    2. Force group membership refresh
    3. Attempt RDP again
    4. Verify access is denied
This confirms permissions are being inherited through group membership rather than direct assignment.

Key Takeaways
    • Permissions should be assigned to groups, not users.
    • Global Groups represent job roles.
    • Domain Local Groups represent resource permissions.
    • AGDLP creates a clear and scalable chain of trust.
    • RBAC simplifies administration, auditing, and access reviews.
    • Enterprise environments rely heavily on group-based access management to reduce complexity and improve security.



# Organizational Units, Groups, Identity, Policy, and Permissions

Overview
One of the concepts that initially confused me while learning Active Directory was the relationship between Organizational Units (OUs) and Security Groups.
My first instinct was to create a separate Organizational Unit for every type of administrator account:
    • Infrastructure Admins
    • Server Admins
    • Security Admins
    • Virtualization Admins
    • Help Desk Admins
At first glance this seems logical, but it is actually a common design mistake.

Understanding Organizational Units
An Organizational Unit (OU) is primarily used to apply administrative control and Group Policy settings to a collection of objects.
In simple terms, an OU answers the question:
"How should these accounts behave?"
Examples of behavior controlled through OUs include:
    • Password policies
    • Administrative templates
    • Security restrictions
    • Logon restrictions
    • Software deployment
    • Certificate enrollment
    • Desktop settings
    • Windows Update settings
An OU does not determine what resources a user can access.

Understanding Security Groups
Security Groups answer a different question:
"What resources should these accounts be allowed to access?"
Examples include:
    • Access to file shares
    • Local administrator rights
    • Remote Desktop access
    • VMware administration
    • Proxmox administration
    • Network equipment administration
    • Password reset permissions
    • Certificate Authority management
Permissions should be assigned through groups, not OUs.

The Common Mistake
A common beginner design looks something like this:
Admins
├── Infrastructure Admins OU
├── Server Admins OU
├── Security Admins OU
├── VM Admins OU
└── Help Desk Admins OU
This structure often creates unnecessary complexity.
If every administrative account should receive the same hardening policies, there is little benefit in creating multiple OUs simply because the administrators perform different jobs.
You end up managing multiple OUs that all receive nearly identical Group Policy Objects (GPOs).

A Better Design
Instead, administrative accounts can be placed into a single administrative OU:
User Accounts
│
└── Admin Accounts
    ├── fritson.infra
    ├── fritson.server
    ├── fritson.security
    ├── fritson.vm
    └── fritson.helpdesk
The OU applies the security controls and hardening policies appropriate for administrative accounts.
Examples:
    • Deny web browsing
    • Restrict software installation
    • Enhanced auditing
    • Credential protection
    • Administrative workstation requirements
    • Strong authentication requirements

Separating Identity from Permissions
The key lesson is understanding the difference between identity and permissions.
Identity
Identity describes who the account represents.
Examples:
    • fritson.infra
    • fritson.server
    • fritson.security
    • fritson.vm
These accounts are administrative identities.
Because they are administrative identities, they belong in the same Admin Accounts OU and receive the same security policies.
Permissions
Permissions describe what the account is allowed to do.
Examples:
    • GG_INFRA_ADMIN
    • GG_SERVER_ADMIN
    • GG_SECURITY_ADMIN
    • GG_VM_ADMIN
These groups determine access to resources.
For example:
fritson.infra
        ↓
GG_INFRA_ADMIN
        ↓
DL_PFSENSE_ADMIN
        ↓
pfSense Administration Rights
and
fritson.server
        ↓
GG_SERVER_ADMIN
        ↓
DL_SERVER_ADMIN
        ↓
Windows Server Administration Rights
The accounts can live in the same OU while having completely different permissions.

Mental Model
A useful way to think about Active Directory is:
Organizational Units
Determine:
    • Policy
    • Management
    • Delegation
    • Behavior
Security Groups
Determine:
    • Access
    • Permissions
    • Resource Authorization

Key Takeaways
    • OUs control how an account behaves.
    • Groups control what an account can access.
    • OUs should be organized around policy requirements.
    • Security Groups should be organized around job roles and permissions.
    • Multiple admin accounts can reside in the same Admin Accounts OU while having different permissions.
    • The question for an OU is:
"What policies should apply to this account?"
    • The question for a Security Group is:
"What permissions should this account have?"
Understanding this distinction is one of the most important concepts in Active Directory design and helps prevent overly complicated OU structures.