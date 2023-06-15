# Access Control List Enumeration 

## Get the ACLs associated with the specified object (groups)
```powershell
Get-ObjectAcl -SamAccountName student1 -ResolveGUIDs
```

## Get the ACLs associated with the specified prefix to be used for search
```powershell
Get-ObjectAcl -ADSprefix 'CN=Administrator,CN=Users' -Verbose
```

## We can also enumerate ACLs using ActiveDirectory module but without resolving GUIDs
```powershell
(Get-Acl "AD:\CN=Administrator, CN=Users, DC=dollarcorp, DC=moneycorp,DC=local").Access
```

## Get the ACLs associated with the specified LDAP path to be used for search
```powershell
Get-ObjectAcl -ADSpath "LDAP://CN=Domain Admins,CN=Users,DC=dollarcorp,DC=moneycorp,DC=local" -ResolveGUIDs -Verbose
```

## Search for interesting ACEs
```powershell
Invoke-ACLScanner -ResolveGUIDs
```

## Get the ACLs associated with the specified path
```powershell
Get-PathAcl -Path "\\dc.mydomain.local\sysvol" 
````
