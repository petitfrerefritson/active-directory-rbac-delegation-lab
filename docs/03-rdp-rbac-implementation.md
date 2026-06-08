
This Organizational Unit structure follows Microsofts AGDLP Methodology. 

This structure was chosen because it increases visibiliy allowing administrators to tell at a glance what accounts have permissions and simplifies the audit process by quickly being able to answer the who, what, where and how. 

In addition to increasing permission visibility it effectively reduces permission creep by only adding users to their assigned roles. Roles that have their permissons managed by a Domain Local Group. Making the administrative overhead when onboarding and offboarding clients lower. 

```text
Accounts
↓
Global Groups
↓
Domain Local Group
↓
Permissions
↓
Resources
```
