# Troubleshooting Guide

## Overview

During the implementation of Role-Based Access Control (RBAC), Organizational Units (OUs), Group Policy Objects (GPOs), and Remote Desktop authorization, several issues were encountered and resolved.

This document captures the troubleshooting process, root causes, corrective actions, and lessons learned throughout the deployment.

---

# Issue 1 – RDP Access Denied Despite Group Membership

## Symptoms

A user was added to the appropriate Global Security Group and the Global Group was nested inside the correct Domain Local Group, however Remote Desktop access was denied.

Error:

```text
The connection was denied because the user account is not authorized for remote login.
```

## Investigation

Verified:

* User account membership
* Global Group membership
* Domain Local Group membership
* OU placement
* Group Policy links

Confirmed:

```text
User
↓
GG_HELPDESK
↓
DL_Workstation_RDP
```

appeared correctly configured.

## Root Cause

The workstation had not yet refreshed group membership information and security token data.

The workstation had not fully processed the updated authorization configuration.

## Resolution

Performed:

```powershell
gpupdate /force
```

and rebooted the workstation.

Retested RDP access.

Result:

PASS

## Lessons Learned

Authorization changes involving nested security groups may require:

* Group Policy refresh
* User logoff/logon
* Workstation reboot

before becoming effective.

---

# Issue 2 – RDP Service Not Available

## Symptoms

Remote Desktop connections failed despite valid credentials.

## Investigation

Verified:

```powershell
Get-Service TermService
```

Checked:

* Firewall rules
* RDP configuration
* Group Policy configuration

## Root Cause

Remote Desktop had not yet been enabled through Group Policy.

## Resolution

Created:

```text
GPO-RDP-Base
```

Configured:

* Enable Remote Desktop
* Allow TCP 3389
* Allow UDP 3389

Applied policy.

Result:

PASS

## Lessons Learned

Authorization alone does not enable Remote Desktop.

RDP requires:

* Service enabled
* Firewall rules configured
* User authorization

All three must exist.

---

# Issue 3 – Group Policy Not Applying

## Symptoms

Expected Group Policy settings were not appearing on target systems.

## Investigation

Executed:

```powershell
gpresult /r
```

Reviewed:

* Applied GPOs
* Security filtering
* OU placement

## Root Cause

The computer object was not located in the Organizational Unit receiving the intended GPO.

## Resolution

Moved the computer object into the correct Organizational Unit.

Forced policy update:

```powershell
gpupdate /force
```

Result:

PASS

## Lessons Learned

Group Policy scope is determined primarily by:

* OU placement
* Security filtering
* WMI filtering

Correct OU placement is critical.

---

# Issue 4 – Helpdesk Access to Administrative Workstations

## Symptoms

Helpdesk accounts attempted to access MGMT01.

Expected behavior:

Access Denied

## Investigation

Verified:

```text
GG_HELPDESK
↓
DL_Workstation_RDP
```

No membership existed within:

```text
DL_Admin_Workstation_RDP
```

## Root Cause

No issue found.

The environment was functioning correctly.

The denial confirmed least-privilege enforcement.

## Resolution

No action required.

Validation recorded.

Result:

PASS

## Lessons Learned

Failed access attempts can be positive validation results when testing authorization boundaries.

---

# Issue 5 – Administrative User Unable to Access MGMT01

## Symptoms

Infrastructure Administrator received:

```text
The connection was denied because the user account is not authorized for remote login.
```

when attempting to access MGMT01.

## Investigation

Reviewed:

* User membership
* Global Group membership
* Domain Local Group membership
* Local Remote Desktop Users group

## Root Cause

Administrative account was not a member of the required authorization path.

Required chain:

```text
User
↓
GG_INFRA_ADMIN
↓
DL_Admin_Workstation_RDP
↓
MGMT01
```

was incomplete.

## Resolution

Restored membership to:

```text
GG_INFRA_ADMIN
```

Refreshed policy.

Retested.

Result:

PASS

## Lessons Learned

AGDLP authorization chains are only as strong as their weakest link.

A missing membership anywhere in the chain results in authorization failure.

---

# Issue 6 – RDP Certificate Trust Warning

## Symptoms

When connecting to MGMT01, Windows displayed:

```text
The identity of the remote computer cannot be verified.
```

## Investigation

Examined the presented certificate.

Observed:

```text
The certificate is not from a trusted certification authority.
```

## Root Cause

The environment had not yet implemented Active Directory Certificate Services (AD CS).

MGMT01 was presenting a certificate that was not trusted by the client.

## Resolution

Accepted the certificate temporarily for testing purposes.

Future implementation will include:

* Active Directory Certificate Services
* Certificate Auto Enrollment
* Enterprise PKI

## Lessons Learned

Authentication and authorization are separate processes.

The user was authorized to access the system.

The certificate warning related only to identity validation of the remote computer.

---

# Issue 7 – Time Synchronization Problems

## Symptoms

Authentication failures occurred after workstation deployment.

## Investigation

Reviewed:

* System clock
* Domain Controller time
* Kerberos requirements

## Root Cause

The workstation system time was significantly different from the Domain Controller.

Kerberos requires closely synchronized clocks.

## Resolution

Corrected system time.

Forced synchronization with the domain.

Result:

PASS

## Lessons Learned

Kerberos authentication is highly dependent on accurate time synchronization.

Always verify time settings before troubleshooting authentication problems.

---

# Key Troubleshooting Principles Learned

* Verify identity before permissions.
* Verify permissions before Group Policy.
* Verify Group Policy before troubleshooting applications.
* Use gpresult to validate policy application.
* Use net localgroup to validate authorization.
* Test both successful and failed access scenarios.
* Authorization and authentication are separate processes.
* Kerberos depends on accurate time synchronization.
* AGDLP simplifies troubleshooting by making permission inheritance visible.

```
```
