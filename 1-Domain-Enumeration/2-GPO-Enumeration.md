# Group Policy Object Enumeration 

## Get list of GPO in current domain.
```powershell
Get-NetGPO
Get-NetGPO -ComputerName dcorp-student1.dollarcorp.moneycorp.local
Get-GPO -All (GroupPolicy module)
Get-GPResultantSetOfPolicy -ReportType Html -Path C:\Users\Administrator\report.html (Provides RSoP)
gpresult /R /V (GroupPolicy Results of current machine)
```

## Get GPO(s) which use Restricted Groups or groups.xml for interesting users
```powershell
Get-NetGPOGroup 
```

## Get users which are in a local group of a machine using GPO
```powershell
Find-GPOComputerAdmin -ComputerName student1.dollarcorp.moneycorp.local
```

## Get machines where the given user is member of a specific group
```powershell
Find-GPOLocation -Username student1 -Verbose
```

## Get OUs in a domain
```powershell
Get-NetOU -FullData
```

## Get GPO applied on an OU. Read GPOname from gplink attribute from Get-NetOU
```powershell
Get-NetGPO -GPOname "{AB306569-220D-43FF-BO3B-83E8F4EF8081}"
Get-GPO -Guid AB306569-220D-43FF-B03B-83E8F4EF8081 (GroupPolicy module) 
```

## Enumerate permissions for GPOs where users with RIDs of > -1000 have some kind of modification/control rights
```
Get-DomainObjectAcl -LDAPFilter '(objectCategory=groupPolicyContainer)' | ? { ($_.SecurityIdentifier -match '^S-1-5-.*-[1-9]\d{3,}$') -and ($_.ActiveDirectoryRights -match 'WriteProperty|GenericAll|GenericWrite|WriteDacl|WriteOwner')}
Get-NetGPO -GPOName '{3E04167E-C2B6-4A9A-8FB7-C811158DC97C}' 
```