## Problem

Improperly organizing Organizational Units (OUs) in Active Directory can create administrative complexity, inconsistent policy application, and increased operational overhead.

For example, placing all users or computers within a single OU makes it difficult to apply different security controls to separate classes of systems and users.

As environments grow, this approach can lead to policy conflicts, reduced visibility, and more complicated administration.

To address these challenges, Organizational Units should be structured according to business function and administrative requirements, allowing policies and delegated administration to be applied in a predictable and scalable manner.

<br>

## Design Objectives

The OU structure was designed to:

- Separate standard and privileged systems
- Simplify Group Policy targeting
- Improve administrative visibility
- Support least-privilege administration
- Reduce operational complexity
- Provide a scalable foundation for future expansion

<br>

## Organizational Unit Structure
```test
DARKHORSE.INTERNAL
│
└── Workstations OU
    │
    ├── GPO-RDP-Base
    │     • Enable RDP
    │     • Allow TCP 3389
    │     • Allow UDP 3389
    │
    ├── Standard OU
    │     │
    │     ├── WS-TEST01
    │     │
    │     └── GPO-Workstation-RDP
    │           • DL_Workstation_RDP
    │
    │
    └── Admin Workstations OU
          │
          ├── MGMT01
          │
          └── GPO-Admin-Workstation-RDP
                • DL_Admin_Workstation_RDP
```


<br>

## Design Rationale

### Standard Workstations OU

The Standard Workstations OU contains user-facing endpoints used for daily business operations.

Separating standard systems into their own OU allows baseline workstation policies to be applied without impacting privileged administrative systems.

Key benefits include:

- Consistent workstation configuration
- Simplified policy management
- Reduced risk of applying privileged settings to user endpoints
- Easier future expansion

<br>

### Admin Workstations OU

The Admin Workstations OU contains systems used for privileged administrative activities.

Administrative workstations require additional security controls because they are frequently used to manage critical infrastructure components such as Active Directory, Group Policy, servers, networking equipment, and virtualization platforms.

Separating these systems allows dedicated hardening policies and administrative restrictions to be applied independently from standard user devices.

Key benefits include:

- Administrative workstation isolation
- Reduced attack surface
- Improved security posture
- Support for least-privilege administration

## Implementation Evidence

<br>

## Key Takeaways

Through this implementation several important Active Directory concepts became clear:

- Organizational Units organize objects and determine policy scope.
- Security Groups determine authorization and access rights.
- Group Policy should be linked to OUs rather than individual systems.
- Separating privileged and non-privileged systems improves security and manageability.
- OU design should support future growth rather than current requirements alone.