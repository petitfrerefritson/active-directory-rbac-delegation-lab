## Problem

Improperly organizing Organizational Units (OUs) in Active Directory can create administrative complexity, inconsistent policy application, and increased operational overhead.

For example, placing all users or computers within a single OU makes it difficult to apply different security controls to separate classes of systems and users.

As environments grow, this approach can lead to policy conflicts, reduced visibility, and more complicated administration.

To address these challenges, Organizational Units should be structured according to business function and administrative requirements, allowing policies and delegated administration to be applied in a predictable and scalable manner.

## Design Objectives

The OU structure was designed to:

- Separate standard and privileged systems
- Simplify Group Policy targeting
- Improve administrative visibility
- Support least-privilege administration
- Reduce operational complexity
- Provide a scalable foundation for future expansion

## Organizational Unit Structure




## OU Hierarchy
```test
Workstations
├── Standard
│   └── WS-TEST01
│
└── Admin Workstations
    └── MGMT01
```
<br>
## Design Rationale

Standard Workstations OU

- Contains user-facing endpoints
- Receives baseline workstation policies
- Intended for day-to-day business use
<br>
Admin Workstations OU

- Contains privileged administrative systems
- Receives additional hardening controls
- Used for infrastructure administration and privileged tasks

