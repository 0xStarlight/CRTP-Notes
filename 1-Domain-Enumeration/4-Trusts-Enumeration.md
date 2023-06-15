# Trusts Enumeration

## Get a list of all domain trusts for the current domain 
```powershell
Get-NetDomainTrust
Get-NetDomainTrust -Domain us.dollarcorp.moneycorp.local
```

## Get details about the current forest
```powershell
Get-NetForest
Get-NetForest -Forest eurocorp.local
```

## Get all domains in the current forest
```powershell
Get-NetForestDomain
Get-NetForestDomain -Forest eurocorp.local
```

## Get all global catalogs for the current forest
```powershell
Get-NetForestCatalog
Get-NetForestCatalog -Forest eurocorp.local
```
 
## Map trusts of a forest
```powershell
Get-NetForestTrust
Get-NetForestTrust -Forest eurocorp.local
```

# PowerView Enumeration [ User Hunting ]

## Find all machines on the current domain where the current user has local admin access
```powershell
Find-LocalAdminAccess -Verbose
```

## Find computers where a domain admin (or specified user/group) has sessions
```powershell
Invoke-UserHunter
Invoke-UserHunter -GroupName "RDPUsers"
```

## To confirm admin access
```powershell
Invoke-UserHunter -CheckAccess
```

## Find computers where a domain admin is logged-in
```powershell
Invoke-UserHunter -Stealth
```

## Get users with privileges in other domains inside the forest
```
Get-DomainForeingUser 
```
## Get groups with privileges in other domains inside the forest
```
Get-DomainForeignGroupMember 
```
