# SELinux Security Lab: User Confinement and Type Enforcement

This repository documents the security configuration and testing performed during the **Jedha cybersecurity course**. The focus is on implementing Mandatory Access Control (MAC) using SELinux on AlmaLinux.

## 1. Project Overview
The objective of this lab is to restrict a standard user (`lbaker`) by mapping them to a confined SELinux role (`staff_u`) and demonstrating that SELinux policies can block execution even when standard Linux permissions (rwx) are present.

## 2. Environment Setup
* **Operating System:** AlmaLinux
* **Administrator User:** `jedha`
* **Confined User:** `lbaker`

## 3. The Bash Script
This is the script used for testing. It was created in `/tmp/test_ls` and given execution permissions.

```bash
#!/bin/bash
# Created as part of Jedha cybersecurity course
ls '$1'
```

## 4. Implementation Steps
4.1. Mapping the Linux User to SELinux Role
We linked the local user lbaker to the staff_u role to apply strict security boundaries.
```bash
sudo /usr/sbin/semanage login -a -s staff_u lbaker
```

4.2. Changing File Security Context
To trigger a security violation, we changed the file's type to tmp_t, a label that the staff_u role is not allowed to execute.
```bash
sudo chcon -t tmp_t /tmp/test_ls
```

## 5. Verification and Results
5.1. Identity Check
After logging in as lbaker, we verified the transition to the confined role:
```bash
id -Z
# Output: staff_u:staff_r:staff_t:s0-s0:c0.c1023
```

5.2. Execution Test
Attempting to run the script as lbaker results in a failure, despite the user owning the file:
```Bash
/tmp/test_ls /etc
```
# Result: Permission denied

## 6. Conclusion
The experiment successfully proved that SELinux Type Enforcement (TE) effectively prevents unauthorized actions. By assigning the staff_u role and using specific type labels, we ensured that the user could not execute scripts in the /tmp directory, significantly hardening the system against internal threats.

