# BloodHound Enumeration

## Enable Sharp-Hound and execute

```powershell
. .\SharpHound.ps1
Invoke-BloodHound -CollectionMethod All,LoggedOn
Invoke-Bloodhound -CollectionMethod All -Domain CONTROLLER.local -ZipFileName loot.zip
```

## To avoid detections like ATA
```powershell
Invoke-BloodHound -CollectionMethod All -ExcludeDC
```

## Start neo4j and BloodHound UI on kali machine and load the zip/json files
```bash
sudo neo4j console&;bloodhound&
```
